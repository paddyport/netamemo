<template>
<li>
	<a :class="['listItem', btnCls, 'trs', btnFlg ? '' : 'isNoActive']" @click="checkClick"><span>{{ btnStr }}</span></a>
</li>
</template>

<script>
export default {
// PT Component
	name: "MenuButton",
	props: {
        btnStr: String,
		btnCls: String,
		btnFlg: Boolean,
	},
	methods: {
		checkClick() {
            this.$emit("GPCallshownLoader");
			this.$emit("GPCallswitchMenuList");
			switch(this.btnCls) {
				case "cur":
					this.$emit("GPCallsetToday", this.btnStr);
					break;
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