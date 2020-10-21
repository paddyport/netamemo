<template>
<div :class="['change', changeMonthFlg ? 'isShown' : '']">
	<ul>
		<li v-for="(cm, ym, cmidx) in changeMonthList" :key="cmidx">
			<a :data-year="ym.slice(0, 4)" :data-month="Number(ym.slice(5))+1" @click="onClick">
				<i>右</i><strong>{{ ym.slice(0, 4) }}年{{ Number(ym.slice(5))+1 }}月</strong>
				<span class="dcm">{{ cm.did.length }}</span>
				<span class="ctg">{{ cm.cid.length }}</span>
			</a>
		</li>
	</ul>
</div>
</template>

<script>
export default {
// PT Component
	name: 'CalendarChange',
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