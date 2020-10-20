<template>
<div id="Posts" v-if="postsFlg" class="posts">
	<div class="content">
		<posts-text
			:markTxtArr="markTxtArr"
			@PostsTxtEdit="openEditTxtMark"
			@PostsTxtView="$listeners['ViewTxtMark']">
		</posts-text>
		<posts-series
			:markSrsArr="markSrsArr"
			@PostsSrsEdit="openEditSrsMark"
			@PostsSrsView="$listeners['ViewSrsMark']">
		</posts-series>
		<p v-if="!markTxtArr.length && !markSrsArr.length" class="note">登録されていません。</p>
	</div>
	<posts-head
		:currentYYMM="currentYYMM"
        :markDate="markDate">
	</posts-head>
	<button type="button" class="icn" @click="onClick">消</button>
</div>
</template>

<script>
import PostsText from './PostsText'
import PostsSeries from './PostsSeries'
import PostsHead from './PostsHead'

export default {
	name: "PostsContainer",
	props: {
        db: Object,
        currentYYMM: Object,
        postsFlg: Boolean,
	},
	data() {
		return {
			markDate: 0,
            markTxtArr: [],
			markSrsArr: [],
			srsArr: {},
		}
	},
	components: {
		PostsText,
		PostsSeries,
        PostsHead,
	},
	methods: {
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		setPostsData(date) {
			this.markDate = date;
			this.markTxtArr = [];
			this.markSrsArr = [];
			this.srsArr = {};
			let that = this,
				_ctime = new Date(that.currentYYMM.year, that.currentYYMM.month, that.markDate).getTime();
			that.db.srs.toArray().then((list) => {
				for(let _data of list) {
					let dtime = new Date(_data.date).getTime();
					that.srsArr[_data.sid] = _data.color;
					if(_ctime === dtime) that.markSrsArr.push(_data);
					that.markSrsArr = that.$parent.compileArrtoArr(that.markSrsArr);
				}
			});
			that.db.txt.toArray().then((list) => {
				for(let _data of list) {
					let dtime = new Date(_data.date).getTime(),
						ltime = new Date(_data.last).getTime(),
						obj = _data;
					obj.color = _data.sid ? that.srsArr[_data.sid] : "transparent";
					if(_ctime === dtime) that.markTxtArr.push(obj);
					if(_ctime === ltime) that.markTxtArr.push(obj);
					that.markTxtArr = that.$parent.compileArrtoArr(that.markTxtArr);
				}
			});
		},
		onClick() {
			this.$emit("PostsCloseClick");
		},
		getDataSrs(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.srs.get(key).then(function(obj){
					resolve(obj);
				});
			});
		},
		async openEditTxtMark(_id) {
			const that = this,
				id = Number(_id);
			let obj = {txt: {}, srs: {}, tag: []};
			for(let data of that.markTxtArr) {
				if(data.tid == id) {
					obj.txt = data;
					obj.txt.body = that.compileBrtoNl(data.body);
					obj.tag = data.tag;
					if(!data.sid) {
						that.$emit("EditTxtMark", obj);
						return;
					}
					obj.srs = await that.getDataSrs(data.sid);
					that.$emit("EditTxtMark", obj);
				}
			}
		},
		getDataTxt(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.txt.where({sid: key}).toArray().then(function(list){
					resolve(list);
				});
			});
		},
		async openEditSrsMark(_id) {
			const that = this,
				id = Number(_id);
			let obj = {txt: [], srs: {}, tag: []};
			for(let data of that.markSrsArr) {
				if(data.sid == id) {
					obj.srs = data;
					obj.srs.body = that.compileBrtoNl(data.body);
					obj.tag = data.tag;
					obj.txt = await that.getDataTxt(data.sid);
					that.$emit("EditSrsMark", obj);
				}
			}
		},
	},
}
</script>

<style>
@import "../../css/posts.css";
</style>