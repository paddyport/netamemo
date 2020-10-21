<template>
<div v-if="editFlg" id="Edit" class="edit">
	<edit-document-container
		v-if="editDcmFlg"
		:db="db"
		:editDcmDcmObj="editDcmDcmObj"
        :editDcmCtgObj="editDcmCtgObj"
        :editDcmTagArr="editDcmTagArr"
        @GPcloseEditDcm="closeEditDcm"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</edit-document-container>
	<edit-category-container
		v-if="editCtgFlg"
		:db="db"
		:editCtgDcmArr="editCtgDcmArr"
        :editCtgCtgObj="editCtgCtgObj"
        :editCtgTagArr="editCtgTagArr"
        @GPcloseEditCtg="closeEditCtg"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</edit-category-container>
</div>
</template>

<script>
import EditDocumentContainer from './document/EditDocumentContainer'
import EditCategoryContainer from './category/EditCategoryContainer'

export default {
// GP Component
	name: "EditContainer",
	props: {
		db: Object,
		editFlg: Boolean,
		editDcmObj: Object,
	},
	data() {
		return {
			editDcmFlg: false,
			editDcmDcmObj: {},
			editDcmCtgObj: {},
			editDcmTagArr: [],
			editCtgFlg: false,
			editCtgDcmArr: {},
			editCtgCtgObj: {},
			editCtgTagArr: [],
		}
	},
	components: {
		EditDocumentContainer,
		EditCategoryContainer,
	},
	methods: {
		setEditDcmData(obj) {
			this.editDcmFlg = true;
			this.editDcmDcmObj = obj.dcm;
			this.editDcmCtgObj = obj.ctg;
			this.editDcmTagArr = obj.tag;
		},
		setEditCtgData(obj) {
			this.editCtgFlg = true;
			this.editCtgDcmArr = obj.dcm;
			this.editCtgCtgObj = obj.ctg;
			this.editCtgTagArr = obj.tag;
		},
		closeEditDcm(id) {
			this.editDcmFlg = false;
			this.editDcmDcmObj = {};
			this.editDcmCtgObj = {};
			this.editDcmTagArr = [];
			this.$emit("ANcloseEditDcm", id);
		},
		closeEditCtg(id) {
			this.editCtgFlg = false;
			this.editCtgDcmArr = [];
			this.editCtgCtgObj = {};
			this.editCtgTagArr = [];
            this.$emit("ANcloseEditCtg", id);
		},
		setSaveTag(arr) {
			let arr1 = [],
				arr2 = [];
			const that = this;
			return new Promise(function(resolve){
				that.db.tag.toArray().then(function(list){
					for(let obj of list) arr1.push(obj.head);
					for(let str of arr) if(arr1.indexOf(str)<0) arr2.push(str);
					resolve(arr2);
				});
			});
		},
		async setSaveTagPromise(arr) {
			const arr1 = await this.setSaveTag(arr);
			const arr2 = arr1.map(function(item){
				return {head: item};
			});
			if(arr2.length) this.db.tag.bulkPut(arr2);
		},
	},
}
</script>

<style scoped>
@import "../../css/edit.css";
</style>