<template>
<div class="series">
	<button type="button" :class="[!Object.keys(DeditTxtSrsObj).length&&!selectSrsnameFlg ? 'btnWord' : 'btnIcon', 'def', 'srs']" @click="switchFlg">
		<span v-if="!Object.keys(DeditTxtSrsObj).length&&!selectSrsnameFlg"><i></i>編集</span>
		<span v-else><i></i></span>
	</button>
	<p v-if="Object.keys(DeditTxtSrsObj).length && !selectSrsnameFlg" :data-sid="DeditTxtSrsObj.sid">
		{{ DeditTxtSrsObj.head }}
		<button type="button" class="icn" @click="remSrsName">消</button>
	</p>
	<div v-if="selectSrsnameFlg" class="field">
		<p>
			<input type="text" placeholder="シリーズ" @focus="suggestSrsName" @keydown="suggestSrsName">
			<button type="button" :class="['btnWord', 'def', 'nml', 'widSM', inputSrsnameFlg ? '' : 'isNoActive']" @click="selectSrsName"><span>追加</span></button>
		</p>
		<ul v-if="suggestSrsArr.length">
			<li v-for="(sgsrs, sgsrsidx) in suggestSrsArr" :key="sgsrsidx">
				<a :data-sid="sgsrs.sid" @click="selectSrsName">{{ sgsrs.head }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
	name: "EditTextSrsname",
	props: {
        db: Object,
		editTxtSrsObj: Object,
	},
	data() {
		return {
			DeditTxtSrsObj: this.editTxtSrsObj,
			selectSrsnameFlg: false,
			inputSrsnameFlg: false,
			suggestSrsArr: [],
			srsLength: 0,
		}
    },
    
	methods: {
		switchFlg() {
            this.selectSrsnameFlg = this.selectSrsnameFlg ? false : true;
        },
        suggestSrsName(e) {
            const that = this,
                val = e.target.value;
            that.db.srs.toArray().then(function(list){
                if(!val) {
					that.suggestSrsArr = list;
					that.inputSrsnameFlg = false;
					that.srsLength = that.suggestSrsArr.length;
                } else {
					that.suggestSrsArr = [];
					that.inputSrsnameFlg = true;
                    for(let obj of list) {
                        if(obj.head.indexOf(val)>-1) that.suggestSrsArr.push(obj);
                    }
                }
            });
        },
        selectSrsName(e) {
			const btn = e.target,
				flg = btn.getAttribute("data-sid"),
				id = flg ? Number(btn.getAttribute("data-sid")) : this.srsLength+1,
				val = flg ? btn.textContent : btn.previousElementSibling.value;
			this.DeditTxtSrsObj.sid = id;
			this.DeditTxtSrsObj.head = val;
			for(let obj of this.suggestSrsArr) {
				if(obj.head==val) {
					this.DeditTxtSrsObj = obj;
				}
			}
			this.suggestSrsArr = [];
			this.selectSrsnameFlg = false;
			this.$emit("SelectEditTxtSrsObj", this.DeditTxtSrsObj);
		},
		remSrsName() {
			this.DeditTxtSrsObj = {};
			this.$emit("SelectEditTxtSrsObj", this.DeditTxtSrsObj);
		},
	},
}
</script>