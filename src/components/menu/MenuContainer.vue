<template>
<div id="Menu" class="menu">
    <ul :class="['list', menuListFlg ? 'isShown' : '']">
		<li><a class="listItem sch trs"><span>フリーワード検索</span></a></li>
		<li><a class="listItem tag trs"><span>タグ検索</span></a></li>
        <menu-anew :dcmNewArr="dcmNewArr"></menu-anew>
        <menu-edit :dcmEdtArr="dcmEdtArr"></menu-edit>
        <menu-document :dcmArr="dcmArr"></menu-document>
        <menu-category :ctgArr="ctgArr"></menu-category>
	</ul>
	<button type="button" :class="['btnIcon', 'def', 'nml', 'menuList', menuListBtnFlg ? '' : 'isNoActive']" @click="callswitchMenuList"><span>メニュー</span></button>
</div>
</template>

<script>
import MenuAnew from './MenuAnew'
import MenuEdit from './MenuEdit'
import MenuDocument from './MenuDocument'
import MenuCategory from './MenuCategory'

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
            dcmArr: [],
            ctgArr: [],
		}
	},
	components: {
		MenuAnew,
		MenuEdit,
		MenuDocument,
		MenuCategory,
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
			that.dcmArr = [];
			that.ctgArr = [];
			that.dcmNewArr = [];
			that.dcmEdtArr = [];
			that.db.dcm.toArray().then((list) => {
				for(let _data of list) {
                    let obj = _data;
                    that.dcmArr.push(_data);
                    obj.datetime = new Date(_data.date).getTime();
                    obj.lasttime = new Date(_data.last).getTime();
					that.dcmNewArr.push(obj);
					that.dcmNewArr.sort(that.$parent.sortDESC("datetime"));
					that.dcmEdtArr.push(obj);
					that.dcmEdtArr.sort(that.$parent.sortDESC("lasttime"));
				}
				if(list.length === that.dcmArr.length) {
					that.dcmNewArr.slice(0, 4);
					that.dcmEdtArr.slice(0, 4);
				}
			});
			that.db.ctg.toArray().then((list) => {
				for(let _data of list) {
                    that.ctgArr.push(_data);
                }
			});
		},
		callswitchMenuList() {
			this.$emit("ANswitchMenuList");
		},
	},
}
</script>

<style>
@import "../../css/menu.css";
</style>