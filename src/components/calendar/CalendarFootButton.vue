<template>
<button type="button" :class="['btnText', 'def', btnCls, 'widLG']" @click="checkClick">
	<span><i></i>{{ btnStr }}</span>
</button>
</template>

<script>
export default {
// PT Component
	name: "CalendarFootButton",
	props: {
		btnStr: String,
		btnCls: String,
	},
	methods: {
		callopenAnewDcm() {
			this.$emit("GPCallopenAnewDcm");
		},
		callopenAnewCtg() {
			this.$emit("GPCallopenAnewCtg");
		},
		checkClick() {
			switch(this.btnCls) {
				case "dcm":
					this.$emit("GPCallopenAnewDcm");
					break;
				case "ctg":
					this.$emit("GPCallopenAnewCtg");
					break;
			}
		},
	},
}
</script>