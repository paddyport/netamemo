<template>
<button type="button" :class="[btnType ? 'btnText' : 'btnIcon', 'def', btnCls, btnType ? 'widLG' : '', btnFlg ? '' : 'isNoActive']" @click="checkClick">
	<span v-if="btnType"><i></i>{{ btnStr }}</span>
	<span v-else><i></i></span>
</button>
</template>

<script>
export default {
// CH Component
	name: "CalendarButton",
	props: {
        btnType: Boolean,
        btnFlg: Boolean,
		btnStr: String,
		btnCls: String,
	},
	methods: {
		checkClick() {
			switch(this.btnCls) {
				case "dcm":
					this.$emit("PTCallopenAnewDcm");
					break;
				case "ctg":
					this.$emit("PTCallopenAnewDcm");
					break;
				case "cur":
					this.$emit("PTCallsetToday", this.btnStr);
					this.$emit("PTCallhiddenChangeMonthMenuList");
                    break;
                default:
                    this.$emit("PTCallswitchChangeMonth");
					break;
			}
		},
	},
}
</script>