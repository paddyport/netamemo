<template>
<li>
	<a :class="['listItem', 'hst', 'acc', dcmNewArr.length ? '' : 'isNoActive']" @click="callaccordionNext"><span>最近新規作成したテキスト</span></a>
	<ul v-if="dcmNewArr.length" class="child">
		<li v-for="(tn, tnidx) in dcmNewArr" :key="tnidx">
			<a class="listItem trs" :data-did="tn.did" @click="callopenViewDcm">
				<p>{{ tn.head }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuAnew",
	props: {
		dcmNewArr: Array,
	},
	methods: {
		callaccordionNext(e) {
			this.$parent.accordionNext(e);
		},
		callopenViewDcm(e) {
			const btn = e.target,
				id = Number(btn.dataset.did);
			this.$emit("GPCallshownLoader");
			this.$emit("GPCallopenViewDcm", id);
			this.$emit("GPCallswitchMenuList");
		},
	},
}
</script>