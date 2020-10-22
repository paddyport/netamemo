<template>
<div class="dcm" :data-did="editDcmDcmObj.did">
    <div class="content">
		<div class="head">
			<div contenteditable="true" @blur="changeEditDcmHead">{{ editDcmDcmObj.head }}</div>
		</div>
		<edit-document-ctgname
			:db="db"
			:editDcmCtgObj="editDcmCtgObj"
			@PTselectEditDcmCtg="selectEditDcmCtg">
		</edit-document-ctgname>
		<edit-document-tags
			:db="db"
			:editDcmTagArr="editDcmTagArr"
			@PTselectEditDcmTag="selectEditDcmTag">
		</edit-document-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditDcmBody">{{ editDcmDcmObj.body }}</div>
		</div>
	</div>
	<edit-foot
		:btnClass="'dcm'"
		:saveFlg="saveFlg"
		@PTcloseEdit="$listeners['GPcloseEditDcm']"
		@PTsaveEdit="setSaveData"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</edit-foot>
</div>
</template>

<script>
import EditDocumentCtgname from './EditDocumentCtgname'
import EditDocumentTags from './EditDocumentTags'
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
		EditDocumentCtgname,
		EditDocumentTags,
		EditFoot,
	},
	data() {
		return {
			DeditDcmDcmObj: this.editDcmDcmObj,
			DeditDcmCtgObj: this.editDcmCtgObj,
			DeditDcmTagArr: this.editDcmTagArr,
			saveFlg: true,
		}
	},
	methods: {
		compileNltoBr(_str) {
			let str = String(_str);
			return str.replace(/<div>/, "\r\n").replace(/<\/div>/, "");
		},
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		changeEditDcmHead(e) {
			const str = e.target.innerText;
			this.saveFlg = !str||!str.match(/\S/g) ? false : true;
			this.DeditDcmDcmObj.head = !str||!str.match(/\S/g) ? "" : this.compileNltoBr(str);
			e.target.innerText = this.compileBrtoNl(this.DeditDcmDcmObj.head);
		},
		changeEditDcmBody(e) {
			const str = e.target.innerText;
			this.DeditDcmDcmObj.body = !str||!str.match(/\S/g) ? "" : this.compileNltoBr(str);
			e.target.innerText = this.compileBrtoNl(this.DeditDcmDcmObj.body);
		},
		selectEditDcmCtg(obj) {
			this.DeditDcmCtgObj = obj;
		},
		selectEditDcmTag(arr) {
			let _arr = arr.concat(arr);
			_arr = new Set(_arr);
			this.DeditDcmTagArr = Array.from(_arr);
		},
		setSaveData() {
			const that = this,
				_d = new Date(),
				last = _d.getFullYear()+"/"+(_d.getMonth()+1)+"/"+_d.getDate(),
				ctgObj = {
					date: last,
					color: "",
					body: "",
				};
			if(that.DeditDcmCtgObj.date) {
				that.DeditDcmCtgObj = Object.assign(that.DeditDcmCtgObj, ctgObj);
			}
			that.db.dcm.update(that.DeditDcmDcmObj.did, {
				cid: that.DeditDcmCtgObj.cid ? that.DeditDcmCtgObj.cid : 0,
				tag: that.DeditDcmTagArr,
				date: that.DeditDcmDcmObj.date,
				last: last,
				head: that.DeditDcmDcmObj.head,
				body: that.DeditDcmDcmObj.body,
			}).then(() => {
				if(that.DeditDcmCtgObj.cid) that.db.ctg.put(that.DeditDcmCtgObj);
				that.$parent.setSaveTagPromise(that.DeditDcmTagArr);
				that.$emit("GPcloseEditDcm", that.DeditDcmDcmObj.did);
				that.$emit("GPCallswitchLoader");
			});
		},
	},
}
</script>