<template>
<div id="Posts" v-if="postsFlg" class="posts">
	<div class="content">
		<ul v-if="sortPostsArr.length" class="list">
            <li v-for="(sp, spidx) in sortPostsArr" :key="spidx">
                <div class="cardItem">
                    <i v-if="sp.cid" :style="{color: sp.color}"></i>
                    <h2>{{ sp.head }}</h2>
                    <time class="date">{{ new Date(sp.date).getFullYear() }}年{{ new Date(sp.date).getMonth()+1 }}月{{ new Date(sp.date).getDate() }}日</time>
                    <time v-if="sp.did" class="last">{{ new Date(sp.last).getFullYear() }}年{{ new Date(sp.last).getMonth()+1 }}月{{ new Date(sp.last).getDate() }}日</time>
                    <p :data-did="sp.did ? sp.did : 0" :data-cid="sp.did ? 0 : sp.cid">
                        <button type="button" :class="['btnIcon', 'def', sp.did ? 'dcm' : 'ctg', 'edt']" @click="openEdit"><span>編集</span></button>
                        <button type="button" :class="['btnIcon', 'def', sp.did ? 'dcm' : 'ctg', 'viw']" @click="openView"><span>確認</span></button>
                        <button type="button" :class="['btnIcon', 'def', sp.did ? 'dcm' : 'ctg', 'rem']" @click="confirmRem"><span>削除</span></button>
                    </p>
                </div>
            </li>
        </ul>
		<p v-else class="note">登録されていません。</p>
	</div>
	<div class="head">
		<h1>{{ postsHead }}</h1>
	</div>
	<button type="button" class="icn" @click="callclosePosts">消</button>
</div>
</template>

<script>
export default {
// GP Component
	name: "PostsContainer",
	props: {
        db: Object,
		postsFlg: Boolean,
		postsHead: String,
        currentYYMM: Object,
	},
	data() {
		return {
			sortPostsArr: [],
		}
	},
	methods: {
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		setPosts(arr) {
			this.sortPostsArr = arr ? arr : this.sortPostsArr;
		},
		callclosePosts() {
			this.$emit("ANclosePosts");
		},
		openEdit(e) {
			const btn = e.target,
				flg = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg",
				id = flg=="dcm" ? btn.parentNode.dataset.did : btn.parentNode.dataset.cid;
			this.$emit("GPCallswitchLoader");
			if(flg=="dcm") {
				this.openEditDcm(id);
			} else {
				this.openEditCtg(id);
			}
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
			for(let data of that.sortPostsArr) {
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
			for(let data of that.sortPostsArr) {
				if(data.cid == id) {
					obj.ctg = data;
					obj.ctg.body = that.compileBrtoNl(data.body);
					obj.tag = data.tag;
					obj.dcm = await that.getDataDcm(data.cid);
					that.$emit("ANopenEditCtg", obj);
				}
			}
		},
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