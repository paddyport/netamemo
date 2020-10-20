<template>
<footer class="footer">
	<button type="button" class="btnText def txt widLG" @click="onClick('txt')"><span><i></i>新規作成</span></button>
	<button type="button" class="btnText def srs widLG" @click="onClick('srs')"><span><i></i>新規作成</span></button>
</footer>
</template>

<script>
export default {
	name: "CalendarFoot",
	methods: {
		onClick(val) {
			this.$emit("FootClick", val);
		},
	},
}
</script>