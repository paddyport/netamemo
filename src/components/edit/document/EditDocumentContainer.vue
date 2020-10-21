<template>
<div class="dcm" :data-did="editDcmDcmObj.did">
    <div class="content">
		<div class="head">
			<div contenteditable="true" @blur="changeEditDcmHead">{{ editDcmDcmObj.head }}</div>
		</div>
		<edit-text-ctgname
			:db="db"
			:editDcmCtgObj="editDcmCtgObj"
			@PTselectEditDcmCtg="selectEditDcmCtg">
		</edit-text-ctgname>
		<edit-text-tags
			:db="db"
			:editDcmTagArr="editDcmTagArr"
			@PTselectEditDcmTag="selectEditDcmTag">
		</edit-text-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditDcmBody">{{ editDcmDcmObj.body }}</div>
		</div>
	</div>
	<edit-foot
		:btnClass="'dcm'"
		@PTcloseEdit="$listeners['GPcloseEditDcm']"
		@PTsaveEdit="setSaveData"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</edit-foot>
</div>
</template>

<script>
import EditTextCtgname from './EditTextCtgname'
import EditTextTags from './EditTextTags'
import EditFoot from '../EditFoot'

export default {
// PT Component
	name: "EditDocumentContainer",
	props: {
		db: Object,
		editDcmDcmObj: Object,
		editDcmCtgObj: Object,
		editDcmTagArr: Array,
	},
	components: {
		EditTextCtgname,
		EditTextTags,
		EditFoot,
	},
	data() {
		return {
			DeditDcmDcmObj: this.editDcmDcmObj,
			DeditDcmCtgObj: this.editDcmCtgObj,
			DeditDcmTagArr: this.editDcmTagArr,
		}
	},
	methods: {
		changeEditDcmHead(e) {
			const str = e.target.textContent;
			this.DeditDcmDcmObj.head = str ? str : this.DeditDcmDcmObj.head;
			e.target.textContent = this.DeditDcmDcmObj.head;
		},
		changeEditDcmBody(e) {
			const str = e.target.textContent;
			this.DeditDcmDcmObj.body = str ? str : this.DeditDcmDcmObj.body;
			e.target.textContent = this.DeditDcmDcmObj.body;
		},
		selectEditDcmCtg(obj) {
			this.DeditDcmCtgObj = obj;
		},
		selectEditDcmTag(arr) {
			let _arr = this.DeditDcmTagArr.concat(arr);
			_arr = new Set(_arr);
			this.DeditDcmTagArr = Array.from(_arr);
		},
		setSaveData() {
			const that = this,
				_d = new Date();
			// tagもputする -> 多分できた
			that.db.dcm.put({
				did: that.DeditDcmDcmObj.did,
				cid: that.DeditDcmCtgObj.cid ? that.DeditDcmCtgObj.cid : null,
				tag: that.DeditDcmTagArr,
				date: that.DeditDcmDcmObj.date,
				last: _d.getFullYear()+"/"+(_d.getMonth()+1)+"/"+_d.getDate(),
				head: that.DeditDcmDcmObj.head,
				body: that.DeditDcmDcmObj.body,
			}).then(() => {
				that.$parent.setSaveTagPromise(that.DeditDcmTagArr);
				that.$emit("GPcloseEditDcm", that.DeditDcmDcmObj.did);
				that.$emit("GPCallswitchLoader");
			});
		},
	},
}
</script>