<template>
<div id="Menu" class="menu">
    <ul :class="['list', menuListFlg ? 'isShown' : '']">
        <li><a class="listItem dcm trs" @click="setPostsDcmData"><span>テキスト一覧</span></a></li>
        <li><a class="listItem ctg trs" @click="setPostsCtgData"><span>カテゴリ一覧</span></a></li>
		<li v-if="false"><a class="listItem sch trs"><span>フリーワード検索</span></a></li>
		<li><a class="listItem tag trs"><span>タグ検索</span></a></li>
        <menu-anew
			:dcmNewArr="dcmNewArr"
			@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallswitchLoader="$listeners['ANswitchLoader']">
		</menu-anew>
        <menu-edit
			:dcmEdtArr="dcmEdtArr"
			@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallswitchLoader="$listeners['ANswitchLoader']">
		</menu-edit>
	</ul>
	<button type="button" :class="['btnIcon', 'def', 'nml', 'menuList', menuListBtnFlg ? '' : 'isNoActive']" @click="$listeners['ANswitchMenuList']"><span>メニュー</span></button>
</div>
</template>

<script>
import MenuAnew from './MenuAnew'
import MenuEdit from './MenuEdit'

export default {
// GP Component
	name: "MenuContainer",
	props: {
        db: Object,
        menuListBtnFlg: Boolean,
        menuListFlg: Boolean,
	},
	data() {
		return {
            dcmNewArr: [],
            dcmEdtArr: [],
		}
	},
	components: {
		MenuAnew,
		MenuEdit,
	},
	created: function(){
		this.setMenuData();
	},
	methods: {
		accordionNext(e) {
			const btn = e.target,
				acc = btn.nextElementSibling;
			if(btn.classList.contains("isOpen")) {
				acc.removeAttribute("style");
				btn.classList.remove("isOpen")
			} else {
				const h = acc.children.length*btn.clientHeight;
				acc.style.height = h+"px";
				btn.classList.add("isOpen")
			}
		},
		setMenuData() {
			const that = this;
			that.dcmNewArr = [];
			that.dcmEdtArr = [];
			that.db.dcm.toArray().then((list) => {
				for(let _data of list) {
                    let obj = _data;
                    obj.date = _data.date;
                    obj.last = _data.last;
					that.dcmNewArr.push(obj);
					that.dcmNewArr.sort(that.$parent.sortDESC("date"));
					that.dcmEdtArr.push(obj);
					that.dcmEdtArr.sort(that.$parent.sortDESC("last"));
				}
				that.dcmNewArr.slice(0, 4);
				that.dcmEdtArr.slice(0, 4);
			});
		},
		getPostsDcmDataCtg() {
			let cols = {};
			const that = this;
			return new Promise(function(resolve){
				that.db.ctg.toArray().then((list) => {
					for(let obj of list) cols[Number(obj.cid)] = obj.color;
					resolve(cols);
				});
			});
		},
		async setPostsDcmData(e) {
			const that = this,
				str = e.target.innerText,
				ctgArr = await that.getPostsDcmDataCtg();
			that.db.dcm.toArray().then((list) => {
				for(let obj of list) {
					const c = obj.cid&&ctgArr[obj.cid] ? ctgArr[obj.cid] : "";
					that.$set(obj, "color", c);
				}
				that.$emit("ANopenPosts", list, str);
				that.$emit("ANswitchMenuList");
				that.$emit("ANswitchLoader");
			});
		},
		setPostsCtgData(e) {
			const that = this,
				str = e.target.innerText;
			that.db.ctg.toArray().then((list) => {
				that.$emit("ANopenPosts", list, str);
				that.$emit("ANswitchMenuList");
				that.$emit("ANswitchLoader");
			});
		},
	},
}
</script>

<style>
@import "../../css/menu.css";
</style>