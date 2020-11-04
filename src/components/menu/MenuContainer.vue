<template>
<div id="Menu" class="menu">
    <ul :class="['list', menuListFlg ? 'isShown' : '']">
		<menu-button
			:btnStr="curStr"
			:btnCls="'cur'"
			:btnFlg="menuCurFlg"
			@GPCallsetToday="$listeners['ANsetToday']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
		<menu-button
			:btnStr="dcmStr"
			:btnCls="'dcm'"
			:btnFlg="menuDcmFlg"
			@GPCallopenPostsDcmAllData="$listeners['ANopenPostsDcmAllData']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
		<menu-button
			:btnStr="ctgStr"
			:btnCls="'ctg'"
			:btnFlg="menuCtgFlg"
			@GPCallopenPostsCtgAllData="$listeners['ANopenPostsCtgAllData']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
		<li v-if="false"><a class="listItem sch trs"><span>フリーワード検索</span></a></li>
		<menu-button
			:btnStr="tagStr"
			:btnCls="'tag'"
			:btnFlg="menuTagFlg"
			@GPCallopenSearchTags="$listeners['ANopenSearchTags']"
			@GPCallswitchMenuList="$listeners['ANswitchMenuList']"
			@GPCallshownLoader="$listeners['ANshownLoader']">
		</menu-button>
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
	<menu-switch
		:menuStr="'メニュー'"
		:menuListBtnFlg="menuListBtnFlg"
		@GPCallswitchMenuList="$listeners['ANswitchMenuList']">
	</menu-switch>
</div>
</template>

<script>
import MenuButton from './MenuButton'
import MenuAccordion from './MenuAccordion'
import MenuSwitch from './MenuSwitch'

export default {
// GP Component
	name: "MenuContainer",
	props: {
        menuListBtnFlg: Boolean,
		menuListFlg: Boolean,
		menuCurFlg: Boolean,
		menuDcmFlg: Boolean,
		menuCtgFlg: Boolean,
		menuTagFlg: Boolean,
		dcmNewArr: Array,
		dcmEdtArr: Array,
	},
	data() {
		return {
			curStr: "今月に戻る",
			dcmStr: "テキスト一覧",
			ctgStr: "カテゴリ一覧",
			tagStr: "タグ検索",
		}
	},
	components: {
		MenuButton,
		MenuAccordion,
		MenuSwitch,
	},
}
</script>

<style>
@import "../../css/menu.css";
</style>