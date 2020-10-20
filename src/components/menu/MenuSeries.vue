<template>
<li>
    <a :class="['listItem', 'srs', 'acc', srsArr.length ? '' : 'isNoActive']" @click="onClickSrs"><span>シリーズ一覧</span></a>
	<ul v-if="srsArr.length" class="child">
		<li v-for="(srs, srsidx) in srsArr" :key="srsidx">
			<a class="listItem trs" :data-sid="srs['sid']">
				<p>{{ srs["head"] }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
	name: "MenuSeries",
	props: {
		srsArr: Array,
	},
	methods: {
		onClickSrs(e) {
			this.$parent.accordionNext(e);
		},
	},
}
</script>