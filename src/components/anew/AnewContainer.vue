<template>
<div v-if="anewFlg" id="Anew" class="anew">
	<anew-document-container
		v-if="anewDcmFlg"
		:db="db"
		:saveFlg="saveFlg"
        @GPCallcloseAnew="$listeners['ANcloseAnew']"
        @GPCallswitchLoader="$listeners['ANswitchLoader']">
	</anew-document-container>
	<anew-category-container
		v-if="anewCtgFlg"
		:db="db"
		:saveFlg="saveFlg"
        @GPCallcloseAnew="$listeners['ANcloseAnew']"
        @GPCallswitchLoader="$listeners['ANswitchLoader']">
	</anew-category-container>
</div>
</template>

<script>
import AnewDocumentContainer from './document/AnewDocumentContainer'
import AnewCategoryContainer from './category/AnewCategoryContainer'

export default {
// GP Component
	name: "AnewContainer",
	props: {
		db: Object,
		anewFlg: Boolean,
	},
	data() {
		return {
            anewDcmFlg: false,
			anewCtgFlg: false,
			saveFlg: false,
		}
	},
	components: {
		AnewDocumentContainer,
		AnewCategoryContainer,
	},
	methods: {
		compileNltoBr(_str) {
			let str = String(_str);
			return str.replace(/<div>/, "\r\n").replace(/<\/div>/, "");
		},
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
        setAnew(str) {
			this.anewDcmFlg = str=="dcm" ? true : false;
            this.anewCtgFlg =  str=="ctg" ? true : false;
			this.saveFlg = false;
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
@import "../../css/anew.css";
</style>