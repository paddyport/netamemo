<template>
<div id="Posts" v-if="postsFlg" class="posts">
	<div class="content">
		<posts-document
			:markDcmArr="markDcmArr"
			@GPopenEditDcm="openEditDcm"
			@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
			@GPCallconfirmRem="$listeners['ANconfirmRem']"
			@GPCallswitchLoader="$listeners['ANswitchLoader']">
		</posts-document>
		<posts-category
			:markCtgArr="markCtgArr"
			@GPopenEditCtg="openEditCtg"
			@GPCallopenViewCtg="$listeners['ANopenViewCtg']"
			@GPCallconfirmRem="$listeners['ANconfirmRem']"
			@GPCallswitchLoader="$listeners['ANswitchLoader']">
		</posts-category>
		<p v-if="!markDcmArr.length && !markCtgArr.length" class="note">登録されていません。</p>
	</div>
	<posts-head
		:currentYYMM="currentYYMM"
        :markDate="markDate">
	</posts-head>
	<button type="button" class="icn" @click="callclosePosts">消</button>
</div>
</template>

<script>
import PostsHead from './PostsHead'
import PostsDocument from './PostsDocument'
import PostsCategory from './PostsCategory'

export default {
// GP Component
	name: "PostsContainer",
	props: {
        db: Object,
        currentYYMM: Object,
        postsFlg: Boolean,
	},
	data() {
		return {
			markDate: 0,
            markDcmArr: [],
			markCtgArr: [],
			ctgArr: {},
		}
	},
	components: {
        PostsHead,
		PostsDocument,
		PostsCategory,
	},
	methods: {
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		setPostsData(date) {
			this.markDate = date;
			this.markDcmArr = [];
			this.markCtgArr = [];
			this.ctgArr = {};
			let that = this,
				_ctime = new Date(that.currentYYMM.year, that.currentYYMM.month, that.markDate).getTime();
			that.db.ctg.toArray().then((list) => {
				for(let _data of list) {
					let dtime = new Date(_data.date).getTime();
					that.ctgArr[_data.cid] = _data.color;
					if(_ctime === dtime) that.markCtgArr.push(_data);
					that.markCtgArr = that.$parent.compileArrtoArr(that.markCtgArr);
				}
			});
			that.db.dcm.toArray().then((list) => {
				for(let _data of list) {
					let dtime = new Date(_data.date).getTime(),
						ltime = new Date(_data.last).getTime(),
						obj = _data;
					obj.color = _data.cid ? that.ctgArr[_data.cid] : "transparent";
					if(_ctime === dtime) that.markDcmArr.push(obj);
					if(_ctime === ltime) that.markDcmArr.push(obj);
					that.markDcmArr = that.$parent.compileArrtoArr(that.markDcmArr);
				}
			});
		},
		callclosePosts() {
			this.$emit("ANclosePosts");
		},
		getDataCtg(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.ctg.get(key).then(function(obj){
					resolve(obj);
				});
			});
		},
		async openEditDcm(_id) {
			const that = this,
				id = Number(_id);
			let obj = {dcm: {}, ctg: {}, tag: []};
			for(let data of that.markDcmArr) {
				if(data.did == id) {
					obj.dcm = data;
					obj.dcm.body = that.compileBrtoNl(data.body);
					obj.tag = data.tag;
					if(!data.cid) {
						that.$emit("ANopenEditDcm", obj);
						return;
					}
					obj.ctg = await that.getDataCtg(data.cid);
					that.$emit("ANopenEditDcm", obj);
				}
			}
		},
		getDataDcm(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.dcm.where({cid: key}).toArray().then(function(list){
					resolve(list);
				});
			});
		},
		async openEditCtg(_id) {
			const that = this,
				id = Number(_id);
			let obj = {dcm: [], ctg: {}, tag: []};
			for(let data of that.markCtgArr) {
				if(data.cid == id) {
					obj.ctg = data;
					obj.ctg.body = that.compileBrtoNl(data.body);
					obj.tag = data.tag;
					obj.dcm = await that.getDataDcm(data.cid);
					that.$emit("ANopenEditCtg", obj);
				}
			}
		},
	},
}
</script>

<style>
@import "../../css/posts.css";
</style>