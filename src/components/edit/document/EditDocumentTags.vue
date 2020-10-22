<template>
<div class="tags">
	<button type="button" :class="[!DeditDcmTagArr.length&&!selectTagFlg ? 'btnWord' : 'btnIcon', 'def', 'tag']" @click="switchFlg">
		<span v-if="!DeditDcmTagArr.length&&!selectTagFlg"><i></i>編集</span>
		<span v-else><i></i></span>
	</button>
	<div v-if="selectTagFlg" class="field">
		<p>
			<input type="text" placeholder="タグ" @focus="suggestTags" @keydown="suggestTags">
			<button type="button" class="btnWord def nml widSM" @click="addSelectTag"><span>追加</span></button>
		</p>
		<ul v-if="suggestTagArr.length">
			<li v-for="(sgtag, sgtagidx) in suggestTagArr" :key="sgtagidx">
				<a :data-tag="sgtag" @click="addSelectTag">{{ sgtag }}</a>
			</li>
		</ul>
	</div>
	<div v-if="DeditDcmTagArr.length" class="area">
		<ul>
			<li v-for="(sltag, sltagidx) in DeditDcmTagArr" :key="sltagidx">
				<a :data-tag="sltag" @click="remSelectTag">{{ sltag }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
// CH Component
	name: "EditDocumentTags",
	props: {
        db: Object,
		editDcmTagArr: Array,
	},
	data() {
		return {
            DeditDcmTagArr: this.editDcmTagArr,
			selectTagFlg: false,
			inputTagFlg: false,
			suggestTagArr: [],
		}
    },
	methods: {
		switchFlg() {
			this.selectTagFlg = this.selectTagFlg ? false : true;
			if(!this.selectTagFlg) this.suggestTagArr = [];
		},
		suggestTags(e) {
			const that = this,
                val = e.target.value;
            that.suggestTagArr = [];
            that.db.tag.toArray().then(function(list){
                if(!val) {
                    for(let obj of list) {
                        if(that.editDcmTagArr.indexOf(obj.head)<0) {
                            that.suggestTagArr.push(obj.head);
                        }
                    }
					that.inputTagFlg = false;
                } else {
					that.inputTagFlg = true;
                    for(let obj of list) {
                        if(that.editDcmTagArr.indexOf(obj.head)<0&&obj.head.indexOf(val)>-1) {
                            that.suggestTagArr.push(obj.head);
                        }
                    }
                }
            });
        },
        addSelectTag(e) {
			const btn = e.target,
				flg = btn.dataset.tag,
                val = flg ? btn.innerText : btn.previousElementSibling.value;
            if(!val || this.DeditDcmTagArr.indexOf(val)>-1) return;
            this.DeditDcmTagArr.push(val);
			if(!flg) {
				btn.previousElementSibling.value = "";
			}
			this.suggestTagArr = [];
			this.$emit("PTselectEditDcmTag", this.DeditDcmTagArr);
        },
        remSelectTag(e) {
            const btn = e.target,
                val = btn.innerText;
            this.DeditDcmTagArr.splice(this.DeditDcmTagArr.indexOf(val), 1);
			this.$emit("PTselectEditDcmTag", this.DeditDcmTagArr);
        },
	},
}
</script>