<template>
<footer class="footer">
	<button type="button" class="btnText def dcm widLG" @click="callopenAnewDcm"><span><i></i>新規作成</span></button>
	<button type="button" class="btnText def ctg widLG" @click="callopenAnewCtg"><span><i></i>新規作成</span></button>
</footer>
</template>

<script>
export default {
// PT Component
	name: "CalendarFoot",
	methods: {
		callopenAnewDcm() {
			this.$emit("GPCallopenAnewDcm");
		},
		callopenAnewCtg() {
			this.$emit("GPCallopenAnewCtg");
		},
	},
}
</script>