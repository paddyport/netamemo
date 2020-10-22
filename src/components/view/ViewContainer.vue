<template>
<div id="View" v-if="viewFlg" class="view">
	<view-document-container
		v-if="viewDcmFlg"
		:viewClass="frontFlg=='dcm'&&viewCtgFlg ? 'front' : ''"
		:viewDcmDcmObj="viewDcmDcmObj"
		:viewDcmCtgObj="viewDcmCtgObj"
		:viewDcmTagArr="viewDcmTagArr"
		:viewDcmBody="viewDcmBody"
		@GPCallopenEditDcm="$listeners['ANopenEditDcm']"
		@GPCallconfirmRem="$listeners['ANconfirmRem']"
		@GPcloseViewDcm="closeViewDcm"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</view-document-container>
	<view-category-container
		v-if="viewCtgFlg"
		:viewClass="frontFlg=='ctg'&&viewDcmFlg ? 'front' : ''"
		:viewCtgCtgObj="viewCtgCtgObj"
		:viewCtgDcmArr="viewCtgDcmArr"
		:viewCtgTagArr="viewCtgTagArr"
		:viewCtgBody="viewCtgBody"
		@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
		@GPCallopenEditCtg="$listeners['ANopenEditCtg']"
		@GPCallconfirmRem="$listeners['ANconfirmRem']"
		@GPcloseViewCtg="closeViewCtg"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</view-category-container>
</div>
</template>

<script>
import ViewDocumentContainer from './document/ViewDocumentContainer'
import ViewCategoryContainer from './category/ViewCategoryContainer'

export default {
// GP Component
	name: "ViewContainer",
	props: {
		db: Object,
		viewFlg: Boolean,
	},
	data() {
		return {
			frontFlg: "",
			viewDcmFlg: false,
			viewDcmDcmObj: {},
			viewDcmCtgObj: {},
			viewDcmTagArr: [],
			viewDcmBody: "",
			viewCtgFlg: false,
			viewCtgCtgObj: {},
			viewCtgDcmArr: [],
			viewCtgTagArr: [],
			viewCtgBody: "",
		}
	},
	components: {
		ViewDocumentContainer,
		ViewCategoryContainer,
	},
	methods: {
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		setDcmData(id, flg) {
			const that = this,
				key = {did: id};
			that.db.dcm.get(key).then((obj) => {
				if(flg) {
					that.viewDcmCtgObj = {};
					if(obj.cid) that.setCtgData(Number(obj.cid));
					that.viewDcmDcmObj = obj;
					that.viewDcmBody = that.compileBrtoNl(obj.body);
					that.viewDcmTagArr = obj.tag;
					that.viewDcmFlg = true;
					that.frontFlg = "dcm";
				} else {
					that.viewCtgDcmArr.push(obj);
				}
			});
		},
		getCtgDataDcm(id) {
			const that = this,
				key = {cid: id};
			that.viewCtgDcmArr = [];
			return new Promise(function(resolve){
				that.db.dcm.where(key).toArray().then((list) => {
					resolve(list);
				});
			});
		},
		async setCtgData(id, flg) {
			const that = this,
				key = {cid: id};
			that.db.ctg.get(key).then((obj) => {
				if(flg) {
					that.viewSCtgDcmArr = [];
					that.viewCtgCtgObj = obj;
					that.viewCtgBody = that.compileBrtoNl(obj.body);
					that.viewCtgTagArr = obj.tag;
					that.viewCtgFlg = true;
					that.frontFlg = "ctg";
				} else {
					that.viewDcmCtgObj = obj;
				}
			});
			that.viewCtgDcmArr = await that.getCtgDataDcm(id);
		},
		checkCloseView() {
			if(!this.viewDcmFlg && !this.viewCtgFlg) {
				this.frontFlg = "";
				this.$emit("ANcloseView");
			}
		},
		closeViewDcm() {
			this.viewDcmDcmObj = {};
			this.viewDcmFlg = false;
			this.frontFlg = "ctg";
			this.checkCloseView();
		},
		closeViewCtg() {
			this.viewCtgCtgObj = {};
			this.viewCtgFlg = false;
			this.frontFlg = "dcm";
			this.checkCloseView();
		},
	},
}
</script>

<style scoped>
@import "../../css/view.css";
</style>