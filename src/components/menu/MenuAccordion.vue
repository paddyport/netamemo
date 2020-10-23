<template>
<li>
	<a :class="['listItem', listCls, 'acc', listArr.length ? '' : 'isNoActive']" @click="callaccordionNext"><span>{{ listStr }}</span></a>
	<ul v-if="listArr.length" class="child">
		<li v-for="(li, liidx) in listArr" :key="liidx">
			<a class="listItem trs" :data-did="li.did" @click="callopenView">
				<p>{{ li.head }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuAccordion",
	props: {
        listArr: Array,
        listStr: String,
        listCls: String,
	},
	methods: {
		callaccordionNext(e) {
			this.$emit("GPCallaccordionNext", e);
		},
		callopenView(e) {
			const btn = e.target,
				id = Number(btn.dataset.did);
			this.$emit("GPCallshownLoader");
			this.$emit("GPCallopenView", id);
			this.$emit("GPCallswitchMenuList");
		},
	},
}
</script>