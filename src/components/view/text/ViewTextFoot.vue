<template>
<div class="foot">
	<button type="button" class="btnText def txt edt widLG" @click="edit"><span><i></i>編集</span></button>
	<button type="button" class="btnText def nml rem widLG"><span><i></i>削除</span></button>
</div>
</template>

<script>
export default {
	name: "ViewTextFoot",
    methods: {
        edit() {
			this.$emit("EditTextClick");
		},
    },
}
</script>