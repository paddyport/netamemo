<template>
<div class="foot">
	<button type="button" class="btnText def nml widLG" @click="closeAnew"><span><span>キャンセル</span></span></button>
	<button type="button" :class="['btnText', 'def', btnClass, 'widLG']" @click="clickSave"><span><i></i>保存</span></button>
</div>
</template>

<script>
export default {
// CH Component
    name: "AnewFoot",
    props: {
        btnClass: String,
    },
	methods: {
        closeAnew() {
            this.$emit("PTcloseAnew");
        },
        clickSave() {
			this.$emit("PTCallswitchLoader");
            // this.$emit("PTsaveEdit");
        },
	},
}
</script>