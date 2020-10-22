<template>
<div class="foot" :data-did="btnClass=='dcm' ? id : 0" :data-cid="btnClass=='ctg' ? id : 0">
	<button type="button" :class="['btnText', 'def', btnClass, 'edt', 'widLG']" @click="callopenEdit"><span><i></i>編集</span></button>
	<button type="button" class="btnText def nml rem widLG" @click="callconfirmRem"><span><i></i>削除</span></button>
</div>
</template>

<script>
export default {
// CH Component
	name: "ViewFoot",
    props: {
        btnClass: String,
        id: Number,
    },
    methods: {
        callopenEdit() {
			this.$emit("PTCallswitchLoader");
			this.$emit("PTcallopenEdit");
		},
        callconfirmRem(e) {
			const btn = e.target;
			btn.parentNode.classList.add("isRemove");
			this.$emit("PTCallconfirmRem", this.btnClass);
		},
    },
}
</script>