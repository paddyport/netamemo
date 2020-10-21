<template>
<div v-if="anewFlg" id="Anew" class="anew">
	<anew-document-container
		v-if="anewDcmFlg"
		:db="db"
        @GPcloseAnewDcm="closeAnewDcm"
        @GPCallswitchLoader="$listeners['ANswitchLoader']">
	</anew-document-container>
</div>
</template>

<script>
import AnewDocumentContainer from './document/AnewDocumentContainer'

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
		}
	},
	components: {
		AnewDocumentContainer,
	},
	methods: {
        setAnewDcm() {
            this.anewDcmFlg = true;
        },
        closeAnewDcm() {
            this.$emit("ANcloseAnewDcm");
        },
	},
}
</script>

<style scoped>
@import "../../css/anew.css";
</style>