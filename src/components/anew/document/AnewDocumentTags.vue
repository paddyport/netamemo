<template>
<div class="tags">
	<button type="button" :class="[!editDcmTagArr.length&&!selectTagFlg ? 'btnWord' : 'btnIcon', 'def', 'tag']" @click="switchFlg">
		<span v-if="!editDcmTagArr.length&&!selectTagFlg"><i></i>編集</span>
		<span v-else><i></i></span>
	</button>
	<div v-if="selectTagFlg" class="field">
		<p>
			<input type="text" placeholder="タグ" @focus="suggestTags" @keypress.delete="suggestTags">
			<button type="button" class="btnWord def nml widSM" @click="addSelectTag"><span>追加</span></button>
		</p>
		<ul v-if="suggestTagArr.length">
			<li v-for="(sgtag, sgtagidx) in suggestTagArr" :key="sgtagidx">
				<a :data-tag="sgtag" @click="addSelectTag">{{ sgtag }}</a>
			</li>
		</ul>
	</div>
	<div v-if="editDcmTagArr.length" class="area">
		<ul>
			<li v-for="(edtag, edtagidx) in editDcmTagArr" :key="edtagidx">
				<a :data-tag="edtag" @click="remSelectTag">{{ edtag }}</a>
			</li>
		</ul>
	</div>
</div>
</template>

<script>
export default {
// CH Component
	name: "AnewDocumentTags",
	props: {
		db: Object,
	},
	data() {
		return {
			editDcmTagArr: [],
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
				flg = btn.getAttribute("data-tag"),
				val = flg ? btn.textContent : btn.previousElementSibling.value;
			if(!val || this.editDcmTagArr.indexOf(val)>-1) return;
			this.editDcmTagArr.push(val);
			if(!flg) {
				btn.previousElementSibling.value = "";
			}
			this.suggestTagArr = [];
			this.$emit("PTselectAnewDcmTag", this.editDcmTagArr);
		},
		remSelectTag(e) {
			const btn = e.target,
				val = btn.textContent;
			this.editDcmTagArr.splice(this.editDcmTagArr.indexOf(val), 1);
			console.log(val, this.editDcmTagArr.indexOf(val));
			this.$emit("PTselectAnewDcmTag", this.editDcmTagArr);
		},
	},
}
</script>