<template>
<div :class="['change', changeMonthFlg ? 'isShown' : '']">
	<ul>
		<li v-for="(cm, ym, cmidx) in changeMonthList" :key="cmidx">
			<a :data-year="ym.slice(0, 4)" :data-month="Number(ym.slice(5))+1" @click="onClick">
				<i>右</i><strong>{{ ym.slice(0, 4) }}年{{ Number(ym.slice(5))+1 }}月</strong>
				<span class="txt">{{ cm["tid"].length }}</span>
				<span class="srs">{{ cm["sid"].length }}</span>
			</a>
		</li>
	</ul>
</div>
</template>

<script>
export default {
	name: 'ChangeMonth',
	props: {
		changeMonthFlg: Boolean,
		changeMonthList: Object
	},
	methods: {
		onClick: function() {
			this.$emit("buttonClick");
		},
	},
}
</script>