<template>
<div class="dcm">
    <div class="content">
		<div class="head">
			<div contenteditable="true" data-placeholder="新規作成タイトル" @blur="changeAnewDcmHead"></div>
		</div>
		<anew-document-ctgname
			:db="db"
			@PTselectAneDcmCtg="selectAnewDcmCtg">
		</anew-document-ctgname>
		<anew-document-tags
			:db="db"
			@PTselectAnewDcmTag="selectAnewDcmTag">
		</anew-document-tags>
		<div class="body">
			<div contenteditable="true" data-placeholder="新規作成コンテンツ" @blur="changeAnewDcmBody"></div>
		</div>
	</div>
    <anew-foot
        :btnCls="'dcm'"
		:AsaveFlg="AsaveFlg"
        @PTCallcloseAnew="$listeners['GPCallcloseAnew']"
		@PTsaveAnew="setSaveData"
        @PTCallshownLoader="$listeners['GPCallshownLoader']">
    </anew-foot>
</div>
</template>

<script>
import AnewDocumentCtgname from './AnewDocumentCtgname'
import AnewDocumentTags from './AnewDocumentTags'
import AnewFoot from '../AnewFoot'

export default {
// PT Component
	name: "AnewDocumentContainer",
	props: {
		db: Object,
		saveFlg: Boolean,
	},
	components: {
		AnewDocumentCtgname,
		AnewDocumentTags,
		AnewFoot,
	},
	data() {
		return {
			anewDcmDcmObj: {},
			anewDcmCtgObj: {},
			anewDcmTagArr: [],
			AsaveFlg: this.saveFlg,
		}
	},
	methods: {
		checkSave(str) {
			this.AsaveFlg = !str||!str.match(/\S/g) ? false : true;
		},
		changeAnewDcmHead(e) {
			const str = e.target.innerText;
			this.checkSave(str);
			this.anewDcmDcmObj.head = !str||!str.match(/\S/g) ? "" : this.$parent.compileNltoBr(str);
			e.target.innerText = this.$parent.compileBrtoNl(this.anewDcmDcmObj.head);
		},
		changeAnewDcmBody(e) {
			const str = e.target.innerText;
			this.anewDcmDcmObj.body = !str||!str.match(/\S/g) ? "" : this.$parent.compileNltoBr(str);
			e.target.innerText = this.$parent.compileBrtoNl(this.anewDcmDcmObj.body);
		},
		selectAnewDcmCtg(obj) {
			this.anewDcmCtgObj = obj;
		},
		selectAnewDcmTag(arr) {
			let _arr = arr.concat(arr);
			_arr = new Set(_arr);
			this.anewDcmTagArr = Array.from(_arr);
		},
		setSaveData() {
			const that = this,
				_d = new Date(),
				date = new Date(_d.getFullYear(), _d.getMonth(), _d.getDate()).getTime(),
				ctgObj = {
					date: date,
					color: "",
					body: "",
				};
			if(!that.anewDcmCtgObj.date) {
				that.anewDcmCtgObj = Object.assign(that.anewDcmCtgObj, ctgObj);
			}
			that.db.dcm.add({
				cid: that.anewDcmCtgObj.cid ? that.anewDcmCtgObj.cid : 0,
				tag: that.anewDcmTagArr,
				date: date,
				last: date,
				head: that.anewDcmDcmObj.head,
				body: that.anewDcmDcmObj.body,
			}).then(() => {
				if(that.anewDcmCtgObj.cid) that.db.ctg.put(that.anewDcmCtgObj);
				that.$parent.setSaveTagPromise(that.anewDcmTagArr);
				that.checkSave();
				that.$emit("GPCallcloseAnew");
				that.$emit("GPCallswitchLoader");
			});
			that.$emit("GPCallswitchLoader");
		},
	},
}
</script>