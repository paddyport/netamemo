<template>
<header class="head">
	<icon-button
		:btnFlg="changeMonthBtnFlg"
		:btnStr="'替'"
		:btnCls="'nml cng'"
		@IconButtonClick="$listeners['GPCallswitchChangeMonth']">
	</icon-button>
	<h1>{{ currentYYMM.year }}年{{ currentYYMM.month+1 }}月</h1>
	<icon-button
		:btnFlg="!(nowYYMM.year==currentYYMM.year&&nowYYMM.month==currentYYMM.month)"
		:btnStr="'今月'"
		:btnCls="'now'"
		@IconButtonClick="changeNowMonth">
	</icon-button>
</header>
</template>

<script>
import IconButton from '../parts/IconButton'

export default {
// PT Component
	name: 'CalendarHead',
	props: {
		nowYYMM: Object,
		currentYYMM: Object,
		changeMonthBtnFlg: Boolean,
	},
	components: {
		IconButton,
	},
	methods: {
		changeNowMonth() {
            this.$emit("GPCallsetToday");
            this.$emit("GPCallhiddenChangeMonthMenuList");
		},
	},
}
</script>