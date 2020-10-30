<template>
<div id="Calendar" class="calendar">
	<calendar-head
		:currentYYMM="currentYYMM"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		@GPCallswitchChangeMonth="$listeners['ANswitchChangeMonth']">
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
	<footer class="footer">
		<calendar-foot-button
			:btnStr="'新規作成'"
			:btnCls="'dcm'"
			@GPCallopenAnewDcm="$listeners['ANopenAnewDcm']">
		</calendar-foot-button>
		<calendar-foot-button
			:btnStr="'新規作成'"
			:btnCls="'ctg'"
			@GPCallopenAnewCtg="$listeners['ANopenAnewCtg']">
		</calendar-foot-button>
	</footer>
</div>
</template>

<script>
import CalendarHead from './CalendarHead'
import CalendarChange from './CalendarChange'
import CalendarBody from './CalendarBody'
import CalendarFootButton from './CalendarFootButton'

export default {
// GP Component
	name: "CalendarContainer",
	props: {
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
		CalendarFootButton,
	},
}
</script>

<style>
@import "../../css/calendar.css";
</style>