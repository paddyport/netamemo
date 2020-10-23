<template>
<div class="series">
	<button type="button" :class="[!Object.keys(anewDcmCtgnameObj).length&&!selectCtgnameFlg ? 'btnWord' : 'btnIcon', 'def', 'ctg']" @click="switchFlg">
		<span v-if="!Object.keys(anewDcmCtgnameObj).length&&!selectCtgnameFlg"><i></i>編集</span>
		<span v-else><i></i></span>
	</button>
	<p v-if="Object.keys(anewDcmCtgnameObj).length&&!selectCtgnameFlg" :data-sid="anewDcmCtgnameObj.cid">{{ anewDcmCtgnameObj.head }}<button type="button" class="icn" @click="remCtgName">消</button></p>
	<div v-if="selectCtgnameFlg" class="field">
		<p>
			<input type="text" placeholder="シリーズ" @focus="suggestCtgname" @keydown="suggestCtgname" @paste="pasteCtgname">
			<button type="button" :class="['btnWord', 'def', 'nml', 'widSM', inputCtgnameFlg ? '' : 'isNoActive']" @click="selectCtgname"><span>追加</span></button>
		</p>
		<ul v-if="suggestCtgnameArr.length">
			<li v-for="(sgctg, sgctgidx) in suggestCtgnameArr" :key="sgctgidx">
				<a :data-cid="sgctg.cid" @click="selectCtgname">{{ sgctg.head }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
// CH Component
	name: "AnewDocumentCtgname",
	props: {
		db: Object,
	},
	data() {
		return {
            selectCtgnameFlg: false,
			inputCtgnameFlg: false,
            anewDcmCtgnameObj: {},
            suggestCtgnameArr: [],
		}
	},
	methods: {
		switchFlg() {
            this.selectCtgnameFlg = this.selectCtgnameFlg ? false : true;
		},
		pasteCtgname() {
			this.inputCtgnameFlg = true;
		},
		suggestCtgname(e) {
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
		selectCtgname(e) {
			const btn = e.target,
				flg = btn.dataset.cid,
				id = flg ? Number(btn.dataset.cid) : this.ctgLength+1,
				val = flg ? btn.innerText : btn.previousElementSibling.value;
			this.anewDcmCtgnameObj.cid = id;
			this.anewDcmCtgnameObj.head = val;
			for(let obj of this.suggestCtgnameArr) {
				if(obj.head==val) {
					this.anewDcmCtgnameObj = obj;
				}
			}
			this.suggestCtgnameArr = [];
			this.selectCtgnameFlg = false;
			this.$emit("PTselectAneDcmCtg", this.anewDcmCtgnameObj);
        },
        remCtgName() {
			this.anewDcmCtgnameObj = {};
			this.$emit("PTselectAneDcmCtg", this.anewDcmCtgnameObj);
        },
	},
}
</script>