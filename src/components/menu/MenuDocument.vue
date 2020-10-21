<template>
<li>
	<a :class="['listItem', 'dcm', 'acc', dcmArr.length ? '' : 'isNoActive']" @click="callaccordionNext"><span>テキスト一覧</span></a>
	<ul v-if="dcmArr.length" class="child">
		<li v-for="(dcm, dcmidx) in dcmArr" :key="dcmidx">
			<a class="listItem trs" :data-did="dcm.did">
				<p>{{ dcm.head }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuDocument",
	props: {
		dcmArr: Array,
	},
	methods: {
		callaccordionNext(e) {
			this.$parent.accordionNext(e);
		},
	},
}
</script>