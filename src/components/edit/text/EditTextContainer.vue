<template>
<div class="txt" :data-tid="editTxtTxtObj.tid">
    <div class="content">
		<div class="head">
			<div contenteditable="true" @blur="changeEditTxtHead">{{ editTxtTxtObj.head }}</div>
		</div>
		<edit-text-srsname
			:db="db"
			:editTxtSrsObj="editTxtSrsObj"
			@SelectEditTxtSrsObj="selectEditTxtSrs">
		</edit-text-srsname>
		<edit-text-tags
			:db="db"
			:editTxtTagArr="editTxtTagArr"
			@SelectEditTxtTagArr="selectEditTxtTag">
		</edit-text-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditTxtBody">{{ editTxtTxtObj.body }}</div>
		</div>
	</div>
	<edit-text-foot
		@FootClickClose="$listeners['FootTxtClick']"
		@FootClickSave="setSaveData"
		@CallSwitchLoaderSave="$listeners['CallSwitchLoader']">
	</edit-text-foot>
</div>
</template>

<script>
import EditTextSrsname from './EditTextSrsname'
import EditTextTags from './EditTextTags'
import EditTextFoot from './EditTextFoot'

export default {
	name: "EditTextContainer",
	props: {
		db: Object,
		editTxtTxtObj: Object,
		editTxtSrsObj: Object,
		editTxtTagArr: Array,
	},
	components: {
		EditTextSrsname,
		EditTextTags,
		EditTextFoot,
	},
	data() {
		return {
			DeditTxtTxtObj: this.editTxtTxtObj,
			DeditTxtSrsObj: this.editTxtSrsObj,
			DeditTxtTagArr: this.editTxtTagArr,
		}
	},
	methods: {
		changeEditTxtHead(e) {
			const str = e.target.textContent;
			this.DeditTxtTxtObj.head = str ? str : this.DeditTxtTxtObj.head;
			e.target.textContent = this.DeditTxtTxtObj.head;
			console.log(str, Boolean(str), this.DeditTxtTxtObj.head);
		},
		changeEditTxtBody(e) {
			const str = e.target.textContent;
			this.DeditTxtTxtObj.body = str ? str : this.DeditTxtTxtObj.body;
			e.target.textContent = this.DeditTxtTxtObj.body;
		},
		selectEditTxtSrs(obj) {
			this.DeditTxtSrsObj = obj;
		},
		selectEditTxtTag(arr) {
			this.DeditTxtTagArr = arr;
		},
		setSaveData() {
			const that = this,
				_d = new Date();
			// srsとtagもputする
			that.db.txt.put({
				tid: that.DeditTxtTxtObj.tid,
				sid: that.DeditTxtSrsObj.sid ? that.DeditTxtSrsObj.sid : null,
				tag: that.DeditTxtTagArr,
				date: that.DeditTxtTxtObj.date,
				last: _d.getFullYear()+"/"+(_d.getMonth()+1)+"/"+_d.getDate(),
				head: that.DeditTxtTxtObj.head,
				body: that.DeditTxtTxtObj.body,
			}).then(() => {
				that.$emit("FootTxtClick", "sav", that.DeditTxtTxtObj.tid);
				that.$emit("CallSwitchLoader");
			});
		},
	},
}
</script>