<template>
<div class="series">
	<button type="button" :class="[!Object.keys(DeditDcmCtgObj).length&&!selectCtgnameFlg ? 'btnWord' : 'btnIcon', 'def', 'ctg']" @click="switchFlg">
		<span v-if="!Object.keys(DeditDcmCtgObj).length&&!selectCtgnameFlg"><i></i>編集</span>
		<span v-else><i></i></span>
	</button>
	<p v-if="Object.keys(DeditDcmCtgObj).length && !selectCtgnameFlg" :data-cid="DeditDcmCtgObj.cid">
		{{ DeditDcmCtgObj.head }}
		<button type="button" class="icn" @click="remCtgName">消</button>
	</p>
	<div v-if="selectCtgnameFlg" class="field">
		<p>
			<input type="text" placeholder="シリーズ" @focus="suggestCtgName" @keydown="suggestCtgName">
			<button type="button" :class="['btnWord', 'def', 'nml', 'widSM', inputCtgnameFlg ? '' : 'isNoActive']" @click="selectCtgName"><span>追加</span></button>
		</p>
		<ul v-if="suggestCtgnameArr.length">
			<li v-for="(sgctg, sgctgidx) in suggestCtgnameArr" :key="sgctgidx">
				<a :data-cid="sgctg.cid" @click="selectCtgName">{{ sgctg.head }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
	name: "EditDocumentCtgname",
	props: {
        db: Object,
		editDcmCtgObj: Object,
	},
	data() {
		return {
			DeditDcmCtgObj: this.editDcmCtgObj,
			selectCtgnameFlg: false,
			inputCtgnameFlg: false,
			suggestCtgnameArr: [],
			ctgLength: 0,
		}
    },
    
	methods: {
		switchFlg() {
            this.selectCtgnameFlg = this.selectCtgnameFlg ? false : true;
        },
        suggestCtgName(e) {
			const that = this,
				val = e.target.value;
			that.db.ctg.toArray().then(function(list){
				if(!val) {
					that.suggestCtgnameArr = list;
					that.inputCtgnameFlg = false;
					that.ctgLength = that.suggestCtgnameArr.length;
				} else {
					that.suggestCtgnameArr = [];
					that.inputCtgnameFlg = true;
					for(let obj of list) {
						if(obj.head.indexOf(val)>-1) that.suggestCtgnameArr.push(obj);
					}
				}
			});
		},
        selectCtgName(e) {
			const btn = e.target,
				flg = btn.dataset.cid,
				id = flg ? Number(btn.dataset.cid) : this.ctgLength+1,
				val = flg ? btn.innerText : btn.previousElementSibling.value;
			this.DeditDcmCtgObj.cid = id;
			this.DeditDcmCtgObj.head = val;
			for(let obj of this.suggestCtgnameArr) {
				if(obj.head==val) this.DeditDcmCtgObj = obj;
			}
			this.suggestCtgnameArr = [];
			this.selectCtgnameFlg = false;
			this.$emit("PTselectEditDcmCtg", this.DeditDcmCtgObj);
		},
		remCtgName() {
			this.DeditDcmCtgObj = {};
			this.$emit("PTselectEditDcmCtg", this.DeditDcmCtgObj);
		},
	},
}
</script>