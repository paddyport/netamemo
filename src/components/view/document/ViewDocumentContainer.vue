<template>
<div :class="['dcm', viewClass]">
	<view-document-content
		:viewDcmHead="viewDcmDcmObj.head"
		:viewDcmBody="viewDcmBody">
	</view-document-content>
	<view-document-ctgname
		v-if="viewDcmDcmObj.cid"
		:viewDcmCtgId="viewDcmCtgObj.cid"
		:viewDcmCtgName="viewDcmCtgObj.head"
		:viewDcmCtgColor="viewDcmCtgObj.color">
	</view-document-ctgname>
	<view-document-tags
		v-if="viewDcmDcmObj.tag.length"
		:viewDcmTagArr="viewDcmTagArr">
	</view-document-tags>
	<view-foot
		:btnClass="'dcm'"
		:id="viewDcmDcmObj.did"
		@PTcallopenEdit="callopenEditDcm"
		@PTCallconfirmRem="$listeners['GPCallconfirmRem']"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</view-foot>
	<button type="button" class="icn" @click="closeViewDcm">消</button>
</div>
</template>

<script>
import ViewDocumentContent from './ViewDocumentContent'
import ViewDocumentCtgname from './ViewDocumentCtgname'
import ViewDocumentTags from './ViewDocumentTags'
import ViewFoot from '../ViewFoot'

export default {
// PT Component
	name: "ViewDocumentContainer",
	props: {
		viewClass: String,
		viewDcmDcmObj: Object,
		viewDcmCtgObj: Object,
		viewDcmTagArr: Array,
		viewDcmBody: String,
	},
	components: {
		ViewDocumentContent,
		ViewDocumentCtgname,
		ViewDocumentTags,
		ViewFoot,
	},
    methods: {
		callopenEditDcm() {
			this.viewDcmDcmObj.body = this.viewDcmBody;
			const obj = {
				dcm: this.viewDcmDcmObj,
				ctg: this.viewDcmCtgObj,
				tag: this.viewDcmTagArr,
			}
			this.$emit("GPCallopenEditDcm", obj);
		},
        closeViewDcm() {
			this.$emit("GPcloseViewDcm");
		},
    },
}
</script>