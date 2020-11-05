<template>
<div id="Calendar" class="calendar">
	<calendar-head
		:nowYYMM="nowYYMM"
		:currentYYMM="currentYYMM"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		@GPCallswitchChangeMonth="$listeners['ANswitchChangeMonth']"
		@GPCallsetToday="$listeners['ANsetToday']"
		@GPCallhiddenChangeMonthMenuList="$listeners['ANhiddenChangeMonthMenuList']">
	</calendar-head>
	<calendar-change
		:currentYYMM="currentYYMM"
		:changeMonthFlg="changeMonthFlg"
		:changeMonthObj="changeMonthObj"
		@GPCallsetCurrent="$listeners['ANsetCurrent']">
	</calendar-change>
	<calendar-body
		:currentDates="currentDates"
		@GPCallsetPostsData="$listeners['ANsetPostsData']"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</calendar-body>
	<calendar-foot
		:btnStr="'新規作成'"
		@GPCallopenAnewDcm="$listeners['ANopenAnewDcm']"
		@GPCallopenAnewCtg="$listeners['ANopenAnewCtg']">
	</calendar-foot>
</div>
</template>

<script>
import CalendarHead from './CalendarHead'
import CalendarChange from './CalendarChange'
import CalendarBody from './CalendarBody'
import CalendarFoot from './CalendarFoot'

export default {
// GP Component
	name: "CalendarContainer",
	props: {
		nowYYMM: Object,
		currentYYMM: Object,
		changeMonthBtnFlg: Boolean,
		changeMonthFlg: Boolean,
		changeMonthObj: Object,
		currentDates: Array,
	},
	components: {
		CalendarHead,
		CalendarChange,
		CalendarBody,
		CalendarFoot,
	},
}
</script>

<style>
@import "../../css/calendar.css";
</style>