<template>
<div class="tags">
	<button type="button" :class="[!DeditTxtTagArr.length&&!selectTagFlg ? 'btnWord' : 'btnIcon', 'def', 'tag', 'nml']" @click="switchFlg">
		<span v-if="!DeditTxtTagArr.length&&!selectTagFlg"><i></i>編集</span>
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
	<div v-if="DeditTxtTagArr.length" class="area">
		<ul>
			<li v-for="(sltag, sltagidx) in DeditTxtTagArr" :key="sltagidx">
				<a :data-tag="sltag" @click="remSelectTag">{{ sltag }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
	name: "EditTextTags",
	props: {
        db: Object,
		editTxtTagArr: Array,
	},
	data() {
		return {
            DeditTxtTagArr: this.editTxtTagArr,
			selectTagFlg: false,
			inputTagFlg: false,
			suggestTagArr: [],
            tagLength: 0,
		}
    },
    
	methods: {
		switchFlg() {
            this.selectTagFlg = this.selectTagFlg ? false : true;
        },
        suggestTags(e) {
            const that = this,
                val = e.target.value;
            that.suggestTagArr = [];
            that.db.tag.toArray().then(function(list){
                if(!val) {
                    for(let obj of list) {
                        if(that.editTxtTagArr.indexOf(obj.head)<0) {
                            that.suggestTagArr.push(obj.head);
                        }
                    }
					that.inputTagFlg = false;
					that.tagLength = that.suggestTagArr.length;
                } else {
					that.inputTagFlg = true;
                    for(let obj of list) {
                        if(that.editTxtTagArr.indexOf(obj.head)<0&&obj.head.indexOf(val)>-1) {
                            that.suggestTagArr.push(obj.head);
                        }
                    }
                }
            });
        },
        addSelectTag(e) {
			const btn = e.target,
				flg = btn.getAttribute("data-tag"),
                val = flg ? btn.textContent : btn.previousElementSibling.value;
            if(this.DeditTxtTagArr.indexOf(val)>-1) return;
            this.DeditTxtTagArr.push(val);
            this.suggestTagArr = [];
			this.$emit("SelectEditTxtTagArr", this.DeditTxtTagArr);
        },
        remSelectTag(e) {
            const btn = e.target,
                val = btn.textContent;
                console.log(this.DeditTxtTagArr.indexOf(val));
            this.DeditTxtTagArr.splice(this.DeditTxtTagArr.indexOf(val), 1);
			this.$emit("SelectEditTxtTagArr", this.DeditTxtTagArr);
        },
	},
}
</script>