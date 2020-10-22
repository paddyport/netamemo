<template>
<div id="Dialog" class="dialog">
    <div class="content">
        <p>選択したデータを削除しますか？</p>
        <p>
            <button type="button" :class="['btnText', 'def', btnClass, 'rem', 'widMD']" @click="removeItem"><span><i></i>削除</span></button>
            <button type="button" class="btnText def nml widMD" @click="closeDialog"><span><span>キャンセル</span></span></button>
        </p>
    </div>
</div>
</template>

<script>
export default {
// GP Component
    name: "DialogContainer",
    props: {
        btnClass: String,
    },
	methods: {
        closeDialog() {
            this.$emit("ANcloseDialog");
        },
        removeItem() {
			this.$emit("ANswitchLoader");
            this.$emit("ANremoveItem");
        },
	},
}
</script>

<style>
@import "../../css/dialog.css";
</style>