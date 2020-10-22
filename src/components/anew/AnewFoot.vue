<template>
<div class="foot">
	<button type="button" class="btnText def nml widLG" @click="callcloseAnew"><span><span>キャンセル</span></span></button>
	<button type="button" :class="['btnText', 'def', btnClass, 'widLG', AsaveFlg ? '' : 'isNoActive']" @click="clickSave"><span><i></i>保存</span></button>
</div>
</template>

<script>
export default {
// CH Component
    name: "AnewFoot",
    props: {
        btnClass: String,
        AsaveFlg: Boolean,
    },
	methods: {
        callcloseAnew() {
            this.$emit("PTCallcloseAnew");
        },
        clickSave() {
			this.$emit("PTCallswitchLoader");
            this.$emit("PTsaveAnew");
        },
	},
}
</script>