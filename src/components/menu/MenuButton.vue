<template>
<button type="button" :class="['btnIcon', 'def', 'nml', 'menuList', menuListBtnFlg ? '' : 'isNoActive']" @click="switchMenuList"><span>{{ menuStr }}</span></button>
</template>

<script>
export default {
// PT Component
	name: "MenuButton",
	props: {
        menuStr: String,
        menuListBtnFlg: Boolean,
	},
	methods: {
		switchMenuList() {
			this.$emit("GPCallswitchMenuList");
		},
	},
}
</script>