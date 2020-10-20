<template>
<header class="head">
	<button type="button" :class="['btnIcon', 'def', 'nml', 'cng', changeMonthBtnFlg ? '' : 'isNoActive']" @click="onClick()"><span>替</span></button>
	<h1>{{ currentYYMM.year }}年{{ currentYYMM.month+1 }}月</h1>
</header>
</template>

<script>
export default {
	name: 'CalendarHead',
	props: {
		currentYYMM: Object,
		changeMonthBtnFlg: Boolean,
	},
	methods: {
		onClick() {
			this.$emit("HeadClick");
		},
	},
}
</script>