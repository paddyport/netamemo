<template>
<header class="head">
	<calendar-button
        :btnType="false"
		:btnFlg="changeMonthBtnFlg"
		:btnStr="'替'"
		:btnCls="'nml cng'"
		@PTCallswitchChangeMonth="$listeners['GPCallswitchChangeMonth']">
	</calendar-button>
	<h1>{{ currentYYMM.year }}年{{ currentYYMM.month+1 }}月</h1>
	<calendar-button
        :btnType="false"
		:btnFlg="!(nowYYMM.year==currentYYMM.year&&nowYYMM.month==currentYYMM.month)"
		:btnStr="'今月'"
		:btnCls="'cur'"
		@PTCallsetToday="$listeners['GPCallsetToday']"
		@PTCallhiddenChangeMonthMenuList="$listeners['GPCallhiddenChangeMonthMenuList']">
	</calendar-button>
</header>
</template>

<script>
import CalendarButton from './CalendarButton'

export default {
// PT Component
	name: 'CalendarHead',
	props: {
		nowYYMM: Object,
		currentYYMM: Object,
		changeMonthBtnFlg: Boolean,
	},
	components: {
		CalendarButton,
	},
}
</script>