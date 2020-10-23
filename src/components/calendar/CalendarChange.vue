<template>
<div :class="['change', changeMonthFlg ? 'isShown' : '']">
	<ul>
		<li v-for="(cm, ym, cmidx) in changeMonthObj" :key="cmidx">
			<a :class="currentYYMM.year==ym.slice(0, 4)&&currentYYMM.month+1==ym.slice(5) ? 'isCurrent' : ''" :data-year="ym.slice(0, 4)" :data-month="ym.slice(5)" @click="callsetCurrent">
				<i>右</i><strong>{{ ym.slice(0, 4) }}年{{ ym.slice(5) }}月</strong>
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
		currentYYMM: Object,
		changeMonthFlg: Boolean,
		changeMonthObj: Object,
	},
	methods: {
		callsetCurrent(e) {
			const btn = e.target,
				yy = Number(btn.dataset.year),
				mm = Number(btn.dataset.month)-1;
			this.$emit("GPCallsetCurrent", {year: yy, month: mm});
		},
	},
}
</script>