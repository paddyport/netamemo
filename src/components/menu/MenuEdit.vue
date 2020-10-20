<template>
<li>
	<a :class="['listItem', 'edt', 'acc', txtEdtArr.length ? '' : 'isNoActive']" @click="onClickEdt"><span>最近編集したテキスト</span></a>
	<ul v-if="txtEdtArr.length" class="child">
		<li v-for="(te, teidx) in txtEdtArr" :key="teidx">
			<a class="listItem trs" :data-tid="te['tid']">
				<p>{{ te["head"] }}</p>
			</a>
		</li>
	</ul>
</li>
</template>

<script>
export default {
	name: "MenuEdit",
	props: {
		txtEdtArr: Array,
	},
	methods: {
		onClickEdt(e) {
			this.$parent.accordionNext(e);
		},
	},
}
</script>