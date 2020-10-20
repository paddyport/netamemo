<template>
<div id="View" v-if="viewFlg" class="view">
	<view-text-container
		v-if="viewTxtFlg"
		:viewClass="frontFlg=='txt'&&viewSrsFlg ? 'front' : ''"
		:viewTxtTxtObj="viewTxtTxtObj"
		:viewTxtSrsObj="viewTxtSrsObj"
		:viewTxtTagArr="viewTxtTagArr"
		:viewTxtBody="viewTxtBody"
		@EditMarkTxtClick="$listeners['EditMarkText']"
		@CloseViewTxtClick="closeViewTxt">
	</view-text-container>
	<view-series-container
		v-if="viewSrsFlg"
		:viewClass="frontFlg=='srs'&&viewTxtFlg ? 'front' : ''"
		:viewSrsSrsObj="viewSrsSrsObj"
		:viewSrsTxtArr="viewSrsTxtArr"
		:viewSrsTagArr="viewSrsTagArr"
		:viewSrsBody="viewSrsBody"
		@ViewMarkTextClick="$listeners['ViewMarkText']"
		@EditMarkSrsClick="$listeners['EditMarkSeries']"
		@CloseViewSrsClick="closeViewSrs">
	</view-series-container>
</div>
</template>

<script>
import ViewTextContainer from './text/ViewTextContainer'
import ViewSeriesContainer from './series/ViewSeriesContainer'

export default {
	name: "ViewContainer",
	props: {
		db: Object,
		viewFlg: Boolean,
	},
	data() {
		return {
			frontFlg: "",
			viewTxtFlg: false,
			viewTxtTxtObj: {},
			viewTxtSrsObj: {},
			viewTxtTagArr: [],
			viewTxtBody: "",
			viewSrsFlg: false,
			viewSrsSrsObj: {},
			viewSrsTxtArr: [],
			viewSrsTagArr: [],
			viewSrsBody: "",
		}
	},
	components: {
		ViewTextContainer,
		ViewSeriesContainer,
	},
	methods: {
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		setTxtData(id, flg) {
			const that = this,
				key = {tid: id};
			that.db.txt.get(key).then((obj) => {
				if(flg) {
					that.viewTxtSrsObj = {};
					if(obj.sid) that.setSrsData(Number(obj.sid));
					that.viewTxtTxtObj = obj;
					that.viewTxtBody = that.compileBrtoNl(obj.body);
					that.viewTxtTagArr = obj.tag;
					that.viewTxtFlg = true;
					that.frontFlg = "txt";
				} else {
					that.viewSrsTxtArr.push(obj);
				}
			});
		},
		getSrsDataTxt(id) {
			const that = this,
				key = {sid: id};
			that.viewSrsTxtArr = [];
			return new Promise(function(resolve){
				that.db.txt.where(key).toArray().then((list) => {
					resolve(list);
				});
			});
		},
		async setSrsData(id, flg) {
			const that = this,
				key = {sid: id};
			that.db.srs.get(key).then((obj) => {
				if(flg) {
					that.viewSrsTxtArr = [];
					that.viewSrsSrsObj = obj;
					that.viewSrsBody = that.compileBrtoNl(obj.body);
					that.viewSrsTagArr = obj.tag;
					that.viewSrsFlg = true;
					that.frontFlg = "srs";
				} else {
					that.viewTxtSrsObj = obj;
				}
			});
			that.viewSrsTxtArr = await that.getSrsDataTxt(id);
		},
		checkCloseView() {
			if(!this.viewTxtFlg && !this.viewSrsFlg) {
				this.frontFlg = "";
				this.$emit("switchViewClick");
			}
		},
		closeViewTxt() {
			this.viewTxtTxtObj = {};
			this.viewTxtFlg = false;
			this.frontFlg = "srs";
			this.checkCloseView();
		},
		closeViewSrs() {
			this.viewSrsSrsObj = {};
			this.viewSrsFlg = false;
			this.frontFlg = "txt";
			this.checkCloseView();
		},
		// viewMarkSrsText(id) {
		// 	this.$emit("", id);
		// },
	},
}
</script>

<style scoped>
@import "../../css/view.css";
</style>