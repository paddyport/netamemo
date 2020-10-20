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
	<div class="foot">
		<button type="button" class="btnText def srs edt widLG"><span><i></i>編集</span></button>
		<button type="button" class="btnText def nml rem widLG"><span><i></i>削除</span></button>
	</div>
	<button type="button" class="icn" @click="closeView">消</button>
</div>
</template>

<script>
import ViewSeriesContent from './ViewSeriesContent'
import ViewSeriesSrsname from './ViewSeriesSrsname'
import ViewSeriesTags from './ViewSeriesTags'

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
	},
    methods: {
        closeView() {
			this.$emit("CloseViewSrsClick");
		},
    },
}
</script>