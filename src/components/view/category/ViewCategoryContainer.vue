<template>
<div :class="['ctg', viewClass]">
	<view-category-content
		:viewCtgHead="viewCtgCtgObj.head"
		:viewCtgBody="viewCtgBody"
		:viewCtgDcmArr="viewCtgDcmArr"
		:viewCtgColor="viewCtgCtgObj.color"
		@PTCallopenViewDcm="$listeners['GPCallopenViewDcm']">
	</view-category-content>
	<view-category-ctgname
		:viewCtgCtgId="viewCtgCtgObj.cid"
		:viewCtgCtgName="viewCtgCtgObj.head"
		:viewCtgCtgColor="viewCtgCtgObj.color">
	</view-category-ctgname>
	<view-category-tags
		v-if="viewCtgCtgObj.tag"
		:viewCtgTagArr="viewCtgTagArr">
	</view-category-tags>
	<view-foot
		:btnClass="'ctg'"
		:id="viewCtgCtgObj.cid"
		@PTcallopenEdit="callopenEditCtg"
		@PTCallconfirmRem="$listeners['GPCallconfirmRem']"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</view-foot>
	<button type="button" class="icn" @click="closeViewCtg">消</button>
</div>
</template>

<script>
import ViewCategoryContent from './ViewCategoryContent'
import ViewCategoryCtgname from './ViewCategoryCtgname'
import ViewCategoryTags from './ViewCategoryTags'
import ViewFoot from '../ViewFoot'

export default {
// PT Component
	name: "ViewCategoryContainer",
	props: {
		viewClass: String,
		viewCtgCtgObj: Object,
		viewCtgDcmArr: Array,
		viewCtgTagArr: Array,
		viewCtgBody: String,
	},
	components: {
		ViewCategoryContent,
		ViewCategoryCtgname,
		ViewCategoryTags,
		ViewFoot,
	},
    methods: {
		callopenEditCtg() {
			const obj = {
				ctg: this.viewCtgCtgObj,
				dcm: this.viewCtgDcmArr,
				tag: this.viewCtgTagArr,
			}
			this.$emit("GPCallopenEditCtg", obj);
		},
        closeViewCtg() {
			this.$emit("GPcloseViewCtg");
		},
    },
}
</script>