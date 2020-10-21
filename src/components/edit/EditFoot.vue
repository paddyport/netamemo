<template>
<div class="foot">
	<button type="button" class="btnText def nml widLG" @click="closeEdit"><span><span>キャンセル</span></span></button>
	<button type="button" :class="['btnText', 'def', btnClass, 'widLG']" @click="onClickSave"><span><i></i>保存</span></button>
</div>
</template>

<script>
export default {
// CH Component
    name: "EditFoot",
    props: {
        btnClass: String,
    },
	methods: {
        closeEdit() {
            this.$emit("PTcloseEdit");
        },
        onClickSave() {
			this.$emit("PTCallswitchLoader");
            this.$emit("PTsaveEdit");
        },
	},
}
</script>