<template>
<div v-if="anewFlg" id="Anew" class="anew">
	<anew-document-container
		v-if="anewDcmFlg"
		:db="db"
        @GPCallcloseAnew="$listeners['ANcloseAnew']"
        @GPCallswitchLoader="$listeners['ANswitchLoader']">
	</anew-document-container>
	<anew-category-container
		v-if="anewCtgFlg"
		:db="db"
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
		}
	},
	components: {
		AnewDocumentContainer,
		AnewCategoryContainer,
	},
	methods: {
        setAnewDcm() {
            this.anewDcmFlg = true;
        },
        setAnewCtg() {
            this.anewCtgFlg = true;
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