<template>
<div id="Menu" class="menu">
    <ul :class="['list', menuListFlg ? 'isShown' : '']">
		<li><a class="listItem sch trs"><span>フリーワード検索</span></a></li>
		<li><a class="listItem tag trs"><span>タグ検索</span></a></li>
        <menu-anew :txtNewArr="txtNewArr"></menu-anew>
        <menu-edit :txtEdtArr="txtEdtArr"></menu-edit>
        <menu-text :txtArr="txtArr"></menu-text>
        <menu-series :srsArr="srsArr"></menu-series>
	</ul>
	<button type="button" :class="['btnIcon', 'def', 'nml', 'menuList', menuListBtnFlg ? '' : 'isNoActive']" @click="onClick"><span>メニュー</span></button>
</div>
</template>

<script>
import MenuAnew from './MenuAnew'
import MenuEdit from './MenuEdit'
import MenuText from './MenuText'
import MenuSeries from './MenuSeries'

export default {
	name: "MenuContainer",
	props: {
        db: Object,
        menuListBtnFlg: Boolean,
        menuListFlg: Boolean,
	},
	data() {
		return {
            txtNewArr: [],
            txtEdtArr: [],
            txtArr: [],
            srsArr: [],
		}
	},
	components: {
		MenuAnew,
		MenuEdit,
		MenuText,
		MenuSeries,
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
			let that = this;
			that.db.txt.toArray().then((list) => {
				for(let _data of list) {
                    let obj = _data;
                    that.txtArr.push(_data);
                    obj.datetime = new Date(_data.date).getTime();
                    obj.lasttime = new Date(_data.last).getTime();
					that.txtNewArr.push(obj);
					that.txtNewArr.sort(that.$parent.sortDESC("datetime"));
					that.txtEdtArr.push(obj);
					that.txtEdtArr.sort(that.$parent.sortDESC("lasttime"));
				}
				if(list.length === that.txtArr.length) {
					that.txtNewArr.slice(0, 4);
					that.txtEdtArr.slice(0, 4);
				}
			});
			that.db.srs.toArray().then((list) => {
				for(let _data of list) {
                    that.srsArr.push(_data);
                }
			});
		},
		onClick() {
			this.$emit("MenuBtnClick");
		},
	},
}
</script>

<style>
@import "../../css/menu.css";
</style>