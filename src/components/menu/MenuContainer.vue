<template>
<div id="Menu" class="menu">
    <ul :class="['list', menuListFlg ? 'isShown' : '']">
		<menu-button
			:btnStr="dcmStr"
			:btnCls="'dcm'"
			@GPmenuBtnClick="setPostsDcmData"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
		<menu-button
			:btnStr="ctgStr"
			:btnCls="'ctg'"
			@GPmenuBtnClick="setPostsCtgData"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
		<li v-if="false"><a class="listItem sch trs"><span>フリーワード検索</span></a></li>
		<menu-button
			:btnStr="tagStr"
			:btnCls="'tag'"
			@GPmenuBtnClick="openSearchTags"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
        <menu-anew
			:dcmNewArr="dcmNewArr"
			@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-anew>
        <menu-edit
			:dcmEdtArr="dcmEdtArr"
			@GPCallopenViewDcm="$listeners['ANopenViewDcm']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-edit>
	</ul>
	<button type="button" :class="['btnIcon', 'def', 'nml', 'menuList', menuListBtnFlg ? '' : 'isNoActive']" @click="$listeners['ANswitchMenuList']"><span>メニュー</span></button>
</div>
</template>

<script>
import MenuButton from './MenuButton'
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
			dcmStr: "テキスト一覧",
			ctgStr: "カテゴリ一覧",
			tagStr: "タグ検索",
            dcmNewArr: [],
            dcmEdtArr: [],
		}
	},
	components: {
		MenuButton,
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
		async setPostsDcmData() {
			const that = this,
				ctgArr = await that.getPostsDcmDataCtg();
			that.db.dcm.toArray().then((list) => {
				for(let obj of list) {
					const c = obj.cid&&ctgArr[obj.cid] ? ctgArr[obj.cid] : "";
					that.$set(obj, "color", c);
				}
				that.$emit("ANopenPosts", list, that.dcmStr);
				that.$emit("ANswitchMenuList");
			});
		},
		setPostsCtgData() {
			const that = this;
			that.db.ctg.toArray().then((list) => {
				that.$emit("ANopenPosts", list, that.ctgStr);
				that.$emit("ANswitchMenuList");
			});
		},
		openSearchTags() {
			const that = this;
			that.db.tag.toArray().then((list) => {
				that.$emit("ANopenSearch", list, that.ctgStr);
				that.$emit("ANswitchMenuList");
			});
		},
	},
}
</script>

<style>
@import "../../css/menu.css";
</style>