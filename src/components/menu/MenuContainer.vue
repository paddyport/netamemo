<template>
<div id="Menu" class="menu">
    <ul :class="['list', menuListFlg ? 'isShown' : '']">
		<menu-link
			:btnStr="dcmStr"
			:btnCls="'dcm'"
			:btnFlg="menuDcmFlg"
			@GPCallopenPostsDcmAllData="$listeners['ANopenPostsDcmAllData']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-link>
		<menu-link
			:btnStr="ctgStr"
			:btnCls="'ctg'"
			:btnFlg="menuCtgFlg"
			@GPCallopenPostsCtgAllData="$listeners['ANopenPostsCtgAllData']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-link>
		<li v-if="false"><a class="listItem sch trs"><span>フリーワード検索</span></a></li>
		<menu-link
			:btnStr="tagStr"
			:btnCls="'tag'"
			:btnFlg="menuTagFlg"
			@GPCallopenSearchTags="$listeners['ANopenSearchTags']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-link>
        <menu-accordion
			:listArr="dcmNewArr"
			:listStr="'最近新規作成したテキスト'"
			:listCls="'hst'"
			@GPCallaccordionNext="$listeners['ANaccordionNext']"
			@GPCallopenView="$listeners['ANopenViewDcm']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-accordion>
        <menu-accordion
			:listArr="dcmEdtArr"
			:listStr="'最近編集したテキスト'"
			:listCls="'edt'"
			@GPCallaccordionNext="$listeners['ANaccordionNext']"
			@GPCallopenView="$listeners['ANopenViewDcm']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-accordion>
	</ul>
	<icon-button
		:btnFlg="menuListBtnFlg"
		:btnStr="'メニュー'"
		:btnCls="'nml menuList'"
		@IconButtonClick="$listeners['ANswitchMenuList']">
	</icon-button>
</div>
</template>

<script>
import MenuLink from './MenuLink'
import MenuAccordion from './MenuAccordion'
import IconButton from '../parts/IconButton'

export default {
// GP Component
	name: "MenuContainer",
	props: {
        menuListBtnFlg: Boolean,
		menuListFlg: Boolean,
		menuDcmFlg: Boolean,
		menuCtgFlg: Boolean,
		menuTagFlg: Boolean,
		dcmNewArr: Array,
		dcmEdtArr: Array,
	},
	data() {
		return {
			dcmStr: "テキスト一覧",
			ctgStr: "カテゴリ一覧",
			tagStr: "タグ検索",
		}
	},
	components: {
		MenuLink,
		MenuAccordion,
		IconButton,
	},
}
</script>

<style>
@import "../../css/menu.css";
</style>