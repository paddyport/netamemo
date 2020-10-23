<template>
<li>
	<a class="listItem tag trs"><span>{{ tagStr }}</span></a>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuTags",
	props: {
		tagStr: String,
	},
	methods: {
		setPostsDcmData() {
			this.$emit("GPCallshownLoader");
			this.$emit("GPopenSearchTags");
		},
	},
}
</script>