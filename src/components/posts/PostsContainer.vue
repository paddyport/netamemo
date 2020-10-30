<template>
<div id="Posts" v-if="postsFlg" class="posts">
	<posts-content
		:postsArr="postsArr"
		@GPCallopenEditDcm="$listeners['ANopenEditDcm']"
		@GPCallopenEditCtg="$listeners['ANopenEditCtg']"
		@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
		@GPCallopenViewCtg="$listeners['ANopenViewCtg']"
		@GPCallconfirmRem="$listeners['ANconfirmRem']"
		@GPCallshownLoader="$listeners['ANshownLoader']">
	</posts-content>
	<posts-head
		:postsHead="postsHead">
	</posts-head>
	<div class="head">
		<h1>{{ postsHead }}</h1>
	</div>
	<button type="button" class="icn" @click="callclosePosts">消</button>
</div>
</template>

<script>
import PostsHead from "./PostsHead"
import PostsContent from "./PostsContent"

export default {
// GP Component
	name: "PostsContainer",
	props: {
        db: Object,
		postsFlg: Boolean,
		postsHead: String,
		postsArr: Array,
        currentYYMM: Object,
	},
	data() {
		return {
			sortPostsArr: [],
		}
	},
	components: {
		PostsHead,
		PostsContent,
	},
	methods: {
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		callclosePosts() {
			this.$emit("ANclosePosts");
		},
		// openEdit(e) {
		// 	const btn = e.target,
		// 		flg = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg",
		// 		id = flg=="dcm" ? btn.parentNode.dataset.did : btn.parentNode.dataset.cid;
		// 	this.$emit("GPCallswitchLoader");
		// 	if(flg=="dcm") {
		// 		this.openEditDcm(id);
		// 	} else {
		// 		this.openEditCtg(id);
		// 	}
		// },
		// getDataCtg(key) {
		// 	const that = this;
		// 	return new Promise(function(resolve){
		// 		that.db.ctg.get(key).then(function(obj){
		// 			resolve(obj);
		// 		});
		// 	});
		// },
		// async openEditDcm(_id) {
		// 	const that = this,
		// 		id = Number(_id);
		// 	let obj = {dcm: {}, ctg: {}, tag: []};
		// 	for(let data of that.sortPostsArr) {
		// 		if(data.did == id) {
		// 			obj.dcm = data;
		// 			obj.dcm.body = that.compileBrtoNl(data.body);
		// 			obj.tag = data.tag;
		// 			if(!data.cid) {
		// 				that.$emit("ANopenEditDcm", obj);
		// 				return;
		// 			}
		// 			obj.ctg = await that.getDataCtg(data.cid);
		// 			that.$emit("ANopenEditDcm", obj);
		// 		}
		// 	}
		// },
		// getDataDcm(key) {
		// 	const that = this;
		// 	return new Promise(function(resolve){
		// 		that.db.dcm.where({cid: key}).toArray().then(function(list){
		// 			resolve(list);
		// 		});
		// 	});
		// },
		// async openEditCtg(_id) {
		// 	const that = this,
		// 		id = Number(_id);
		// 	let obj = {dcm: [], ctg: {}, tag: []};
		// 	for(let data of that.sortPostsArr) {
		// 		if(data.cid == id) {
		// 			obj.ctg = data;
		// 			obj.ctg.body = that.compileBrtoNl(data.body);
		// 			obj.tag = data.tag;
		// 			obj.dcm = await that.getDataDcm(data.cid);
		// 			that.$emit("ANopenEditCtg", obj);
		// 		}
		// 	}
		// },
		openView(e) {
			const btn = e.target,
				flg = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg",
				id = flg=="dcm" ? Number(btn.parentNode.dataset.did) : Number(btn.parentNode.dataset.cid);
			this.$emit("GPCallswitchLoader");
			if(flg=="dcm") {
				this.$emit("ANopenViewDcm", id);
			} else {
				this.$emit("ANopenViewCtg", id);
			}
		},
		confirmRem(e) {
			const btn = e.target,
				btncls = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg";
			btn.parentNode.classList.add("isRemove");
			this.$emit("ANconfirmRem", btncls);
		},
	},
}
</script>

<style>
@import "../../css/posts.css";
</style>