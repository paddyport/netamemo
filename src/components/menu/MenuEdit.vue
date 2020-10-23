<template>
<li>
	<a :class="['listItem', 'edt', 'acc', dcmEdtArr.length ? '' : 'isNoActive']" @click="callaccordionNext"><span>最近編集したテキスト</span></a>
	<ul v-if="dcmEdtArr.length" class="child">
		<li v-for="(de, deidx) in dcmEdtArr" :key="deidx">
			<a class="listItem trs" :data-did="de.did" @click="callopenViewDcm">
				<p>{{ de.head }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuEdit",
	props: {
		dcmEdtArr: Array,
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