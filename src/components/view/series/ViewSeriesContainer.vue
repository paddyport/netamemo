<template>
<div :class="['srs', viewClass]">
	<view-series-content
		:viewSrsHead="viewSrsSrsObj.head"
		:viewSrsBody="viewSrsBody"
		:viewSrsTxtArr="viewSrsTxtArr"
		:viewSrsColor="viewSrsSrsObj.color"
		@ItemClickView="$listeners['ViewMarkTextClick']">
	</view-series-content>
	<view-series-srsname
		:viewSrsSrsId="viewSrsSrsObj.sid"
		:viewSrsSrsName="viewSrsSrsObj.head"
		:viewSrsSrsColor="viewSrsSrsObj.color">
	</view-series-srsname>
	<view-series-tags
		v-if="viewSrsSrsObj.tag"
		:viewSrsTagArr="viewSrsTagArr">
	</view-series-tags>
	<view-series-foot
		@EditSeriesClick="editSeries">
	</view-series-foot>
	<button type="button" class="icn" @click="closeView">消</button>
</div>
</template>

<script>
import ViewSeriesContent from './ViewSeriesContent'
import ViewSeriesSrsname from './ViewSeriesSrsname'
import ViewSeriesTags from './ViewSeriesTags'
import ViewSeriesFoot from './ViewSeriesFoot'

export default {
	name: "ViewSeriesContainer",
	props: {
		viewClass: String,
		viewSrsSrsObj: Object,
		viewSrsTxtArr: Array,
		viewSrsTagArr: Array,
		viewSrsBody: String,
	},
	components: {
		ViewSeriesContent,
		ViewSeriesSrsname,
		ViewSeriesTags,
		ViewSeriesFoot,
	},
    methods: {
		editSeries() {
			console.log(this.viewSrsTxtArr);
			const obj = {
				srs: this.viewSrsSrsObj,
				txt: this.viewSrsTxtArr,
				tag: this.viewSrsTagArr,
			}
			this.$emit("EditMarkSrsClick", obj);
		},
        closeView() {
			this.$emit("CloseViewSrsClick");
		},
    },
}
</script>