<template>
<button type="button" :class="['btnText', 'def', btnCls, btnSize ? btnSize : '']" @click="onClick">
    <span v-if="iconFlg"><i></i>{{ btnStr }}</span>
    <span v-else><span>{{ btnStr }}</span></span>
</button>
</template>

<script>
export default {
// SU Component
    name: "TextButton",
    props: {
        iconFlg: Boolean,
		btnStr: String,
        btnCls: String,
        btnSize: String,
    },
	methods: {
		onClick() {
            this.$emit("TextButtonClick");
		},
	},
}
</script>