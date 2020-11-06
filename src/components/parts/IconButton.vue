<template>
<button type="button" :class="['btnIcon', 'def', btnCls, btnFlg ? '' : 'isNoActive']" @click="onClick">
    <span v-if="btnStr"><i></i>{{ btnStr }}</span>
    <span v-else><i></i></span>
</button>
</template>

<script>
export default {
// SU Component
    name: "IconButton",
    props: {
        btnFlg: Boolean,
		btnStr: String,
        btnCls: String,
    },
	methods: {
		onClick() {
            this.$emit("IconButtonClick");
		},
	},
}
</script>