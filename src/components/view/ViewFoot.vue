<template>
<div class="foot">
	<button type="button" :class="['btnText', 'def', btnClass, 'edt', 'widLG']" @click="edit"><span><i></i>編集</span></button>
	<button type="button" class="btnText def nml rem widLG"><span><i></i>削除</span></button>
</div>
</template>

<script>
export default {
// CH Component
	name: "ViewFoot",
    props: {
        btnClass: String,
    },
    methods: {
        edit() {
			this.$emit("PTCallswitchLoader");
			this.$emit("PTcallopenEdit");
		},
    },
}
</script>