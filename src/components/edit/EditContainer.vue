<template>
<div v-if="editFlg" id="Edit" class="edit">
	<edit-text-container
		v-if="editTxtFlg"
		:db="db"
		:editTxtTxtObj="editTxtTxtObj"
        :editTxtSrsObj="editTxtSrsObj"
        :editTxtTagArr="editTxtTagArr"
        @FootTxtClick="editTxtFootClick"
		@CallSwitchLoader="$listeners['SwitchLoader']">
	</edit-text-container>
</div>
</template>

<script>
import EditTextContainer from './text/EditTextContainer'

export default {
	name: "EditContainer",
	props: {
		db: Object,
		editFlg: Boolean,
		editTxtObj: Object,
	},
	data() {
		return {
			editTxtFlg: false,
			editTxtTxtObj: {},
			editTxtSrsObj: {},
			editTxtTagArr: [],
		}
	},
	components: {
		EditTextContainer,
	},
	methods: {
		setEditTxtData(obj) {
			this.editTxtFlg = true;
			this.editTxtTxtObj = obj.txt;
			this.editTxtSrsObj = obj.srs;
			this.editTxtTagArr = obj.tag;
		},
		editTxtFootClick(...args) {
			const [val, id] = args;
			if(val=="cls" || val=="sav") {
				this.editTxtFlg = false;
				this.editTxtTxtObj = {};
				this.editTxtSrsObj = {};
				this.editTxtTagArr = [];
				if(val=="cls") this.$emit("CloseEditClick");
                if(val=="sav") this.$emit("CloseEditClick", id);
			}
		},
	},
}
</script>

<style scoped>
@import "../../css/edit.css";
</style>