<template>
<li>
	<a class="listItem ctg trs" @click="setPostsCtgData"><span>{{ ctgStr }}</span></a>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuCategory",
	props: {
		ctgStr: String,
	},
	methods: {
		setPostsCtgData() {
			this.$emit("GPCallshownLoader");
			this.$emit("GPsetPostsCtgData");
		},
	},
}
</script>