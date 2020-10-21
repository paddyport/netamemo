<template>
<li>
    <a :class="['listItem', 'ctg', 'acc', ctgArr.length ? '' : 'isNoActive']" @click="callaccordionNext"><span>カテゴリ一覧</span></a>
	<ul v-if="ctgArr.length" class="child">
		<li v-for="(ctg, ctgidx) in ctgArr" :key="ctgidx">
			<a class="listItem trs" :data-cid="ctg.cid">
				<p>{{ ctg.head }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuCategory",
	props: {
		ctgArr: Array,
	},
	methods: {
		callaccordionNext(e) {
			this.$parent.accordionNext(e);
		},
	},
}
</script>