<template>
<li>
	<a :class="['listItem', btnCls, 'trs']" @click="checkClick"><span>{{ btnStr }}</span></a>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuButton",
	props: {
        btnStr: String,
        btnCls: String,
	},
	methods: {
		checkClick() {
            this.$emit("GPCallshownLoader");
            this.$emit("GPmenuBtnClick");
		},
	},
}
</script>