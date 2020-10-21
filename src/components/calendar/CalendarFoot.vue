<template>
<footer class="footer">
	<button type="button" class="btnText def dcm widLG" @click="callopenAnewField('dcm')"><span><i></i>新規作成</span></button>
	<button type="button" class="btnText def ctg widLG" @click="callopenAnewField('ctg')"><span><i></i>新規作成</span></button>
</footer>
</template>

<script>
export default {
// PT Component
	name: "CalendarFoot",
	methods: {
		callopenAnewField(val) {
			this.$emit("GPCallopenAnewField", val);
		},
	},
}
</script>