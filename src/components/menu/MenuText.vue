<template>
<li>
	<a :class="['listItem', 'txt', 'acc', txtArr.length ? '' : 'isNoActive']" @click="onClickTxt"><span>テキスト一覧</span></a>
	<ul v-if="txtArr.length" class="child">
		<li v-for="(txt, txtidx) in txtArr" :key="txtidx">
			<a class="listItem trs" :data-tid="txt['tid']">
				<p>{{ txt["head"] }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
	name: "MenuText",
	props: {
		txtArr: Array,
	},
	methods: {
		onClickTxt(e) {
			this.$parent.accordionNext(e);
		},
	},
}
</script>