<template>
<div class="foot">
	<button type="button" class="btnText def nml widLG" @click="onClickClose"><span><span>キャンセル</span></span></button>
	<button type="button" class="btnText def txt widLG" @click="onClickSave"><span><i></i>保存</span></button>
</div>
</template>

<script>
export default {
	name: "EditTextFoot",
	methods: {
        onClickClose() {
            this.$emit("FootClickClose", "cls");
        },
        onClickSave() {
			this.$emit("CallSwitchLoaderSave");
            this.$emit("FootClickSave", "sav");
        },
	},
}
</script>