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
	<edit-series-container
		v-if="editSrsFlg"
		:db="db"
		:editSrsTxtArr="editSrsTxtArr"
        :editSrsSrsObj="editSrsSrsObj"
        :editSrsTagArr="editSrsTagArr"
		@FootSrsClick="editSrsFootClick"
		@CallSwitchLoader="$listeners['SwitchLoader']">
	</edit-series-container>
</div>
</template>

<script>
import EditTextContainer from './text/EditTextContainer'
import EditSeriesContainer from './series/EditSeriesContainer'

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
			editSrsFlg: false,
			editSrsTxtArr: {},
			editSrsSrsObj: {},
			editSrsTagArr: [],
		}
	},
	components: {
		EditTextContainer,
		EditSeriesContainer,
	},
	methods: {
		setEditTxtData(obj) {
			this.editTxtFlg = true;
			this.editTxtTxtObj = obj.txt;
			this.editTxtSrsObj = obj.srs;
			this.editTxtTagArr = obj.tag;
		},
		setEditSrsData(obj) {
			this.editSrsFlg = true;
			this.editSrsTxtArr = obj.txt;
			this.editSrsSrsObj = obj.srs;
			this.editSrsTagArr = obj.tag;
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
		editSrsFootClick(...args) {
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