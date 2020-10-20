<template>
<div :class="['txt', viewClass]">
	<view-text-content
		:viewTxtHead="viewTxtTxtObj.head"
		:viewTxtBody="viewTxtBody">
	</view-text-content>
	<view-text-srsname
		v-if="viewTxtTxtObj.sid"
		:viewTxtSrsId="viewTxtSrsObj.sid"
		:viewTxtSrsName="viewTxtSrsObj.head"
		:viewTxtSrsColor="viewTxtSrsObj.color">
	</view-text-srsname>
	<view-text-tags
		v-if="viewTxtTxtObj.tag.length"
		:viewTxtTagArr="viewTxtTagArr">
	</view-text-tags>
	<view-text-foot
		@EditTextClick="editText">
	</view-text-foot>
	<button type="button" class="icn" @click="closeView">消</button>
</div>
</template>

<script>
import ViewTextContent from './ViewTextContent'
import ViewTextSrsname from './ViewTextSrsname'
import ViewTextTags from './ViewTextTags'
import ViewTextFoot from './ViewTextFoot'

export default {
	name: "ViewTextContainer",
	props: {
		viewClass: String,
		viewTxtTxtObj: Object,
		viewTxtSrsObj: Object,
		viewTxtTagArr: Array,
		viewTxtBody: String,
	},
	components: {
		ViewTextContent,
		ViewTextSrsname,
		ViewTextTags,
		ViewTextFoot,
	},
    methods: {
		editText() {
			this.viewTxtTxtObj.body = this.viewTxtBody;
			const obj = {
				txt: this.viewTxtTxtObj,
				srs: this.viewTxtSrsObj,
				tag: this.viewTxtTagArr,
			}
			this.$emit("EditMarkTextClick", obj);
		},
        closeView() {
			console.log("a");
			this.$emit("CloseViewTxtClick");
		},
    },
}
</script>