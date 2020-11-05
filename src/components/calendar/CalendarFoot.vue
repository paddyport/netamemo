<template>
<footer class="footer">
	<calendar-button
        :btnType="true"
        :btnFlg="true"
		:btnStr="btnStr"
		:btnCls="'dcm'"
		@PTCallopenAnewDcm="$listeners['GPCallopenAnewCtg']">
	</calendar-button>
	<calendar-button
        :btnType="true"
        :btnFlg="true"
		:btnStr="btnStr"
		:btnCls="'ctg'"
		@PTCallopenAnewDcm="$listeners['GPCallopenAnewCtg']">
	</calendar-button>
</footer>
</template>

<script>
import CalendarButton from './CalendarButton'

export default {
// PT Component
	name: "CalendarFoot",
	props: {
		btnStr: String,
	},
	components: {
		CalendarButton,
	},
}
</script>