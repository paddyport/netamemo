<template>
<div v-if="anewFlg" id="Anew" class="anew">
	<anew-text-container
		v-if="anewDcmFlg"
		:db="db"
		:anewDcmDcmObj="anewDcmDcmObj"
        :anewDcmCtgObj="anewDcmCtgObj"
        :anewDcmTagArr="anewDcmTagArr"
        @GPcloseAnewDcm="closeAnewDcm"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</anew-text-container>
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
		}
	},
	components: {
		EditTextContainer,
	},
	methods: {
	},
}
</script>

<style scoped>
@import "../../css/anew.css";
</style>