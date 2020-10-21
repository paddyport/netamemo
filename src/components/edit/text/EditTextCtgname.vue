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
		<ul v-if="suggestCtgArr.length">
			<li v-for="(sgctg, sgctgidx) in suggestCtgArr" :key="sgctgidx">
				<a :data-cid="sgctg.cid" @click="selectCtgName">{{ sgctg.head }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
	name: "EditTextCtgname",
	props: {
        db: Object,
		editDcmCtgObj: Object,
	},
	data() {
		return {
			DeditDcmCtgObj: this.editDcmCtgObj,
			selectCtgnameFlg: false,
			inputCtgnameFlg: false,
			suggestCtgArr: [],
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
					that.suggestCtgArr = list;
					that.inputCtgnameFlg = false;
					that.ctgLength = that.suggestCtgArr.length;
                } else {
					that.suggestCtgArr = [];
					that.inputCtgnameFlg = true;
                    for(let obj of list) {
                        if(obj.head.indexOf(val)>-1) that.suggestCtgArr.push(obj);
                    }
                }
            });
        },
        selectCtgName(e) {
			const btn = e.target,
				flg = btn.getAttribute("data-cid"),
				id = flg ? Number(btn.getAttribute("data-cid")) : this.ctgLength+1,
				val = flg ? btn.textContent : btn.previousElementSibling.value;
			this.DeditDcmCtgObj.cid = id;
			this.DeditDcmCtgObj.head = val;
			for(let obj of this.suggestCtgArr) {
				if(obj.head==val) {
					this.DeditDcmCtgObj = obj;
				}
			}
			this.suggestCtgArr = [];
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