<template>
<footer class="footer">
	<text-button
        :iconFlg="true"
        :btnFlg="true"
		:btnStr="btnStr"
		:btnCls="'dcm'"
		:btnSize="'widLG'"
		@TextButtonClick="$listeners['GPCallopenAnewDcm']">
	</text-button>
	<text-button
        :iconFlg="true"
        :btnFlg="true"
		:btnStr="btnStr"
		:btnCls="'ctg'"
		:btnSize="'widLG'"
		@TextButtonClick="$listeners['GPCallopenAnewCtg']">
	</text-button>
</footer>
</template>

<script>
import TextButton from '../parts/TextButton'

export default {
// PT Component
	name: "CalendarFoot",
	props: {
		btnStr: String,
	},
	components: {
		TextButton,
	},
}
</script>