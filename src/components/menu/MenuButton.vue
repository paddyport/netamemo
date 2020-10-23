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
			this.$emit("GPCallswitchMenuList");
			switch(this.btnCls) {
				case "dcm":
					this.$emit("GPCallopenPostsDcmAllData", this.btnStr);
					break;
				case "ctg":
					this.$emit("GPCallopenPostsCtgAllData", this.btnStr);
					break;
				case "tag":
					this.$emit("GPCallopenSearchTags", this.btnStr);
					break;
			}
		},
	},
}
</script>