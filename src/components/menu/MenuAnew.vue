<template>
<li>
	<a :class="['listItem', 'hst', 'acc', txtNewArr.length ? '' : 'isNoActive']" @click="onClickNew"><span>最近新規作成したテキスト</span></a>
	<ul v-if="txtNewArr.length" class="child">
		<li v-for="(tn, tnidx) in txtNewArr" :key="tnidx">
			<a class="listItem trs" :data-tid="tn['tid']">
				<p>{{ tn["head"] }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
	name: "MenuAnew",
	props: {
		txtNewArr: Array,
	},
	methods: {
		onClickNew(e) {
			this.$parent.accordionNext(e);
		},
	},
}
</script>