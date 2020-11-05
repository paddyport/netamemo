<template>
<button type="button" :class="['btnText', 'def', btnCls, btnType ? 'rem' : '', 'widMD']" @click="checkClick">
    <span v-if="btnType"><i></i>{{ btnStr }}</span>
    <span v-else><span>{{ btnStr }}</span></span>
</button>
</template>

<script>
export default {
// PT Component
    name: "DialogButton",
    props: {
        btnType: Boolean,
		btnStr: String,
        btnCls: String,
    },
	methods: {
		checkClick() {
			switch(this.btnType) {
				case true:
					this.$emit("GPCallshownLoader");
					this.$emit("GPCallremoveItem");
					break;
                default:
                    this.$emit("GPCallcloseDialog");
					break;
			}
		},
	},
}
</script>