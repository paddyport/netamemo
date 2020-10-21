<template>
<div class="dcm" :data-did="editDcmDcmObj.did">
    <div class="content">
		<div class="head">
			<div contenteditable="true"></div>
		</div>
		<edit-text-ctgname
			:db="db"
			:editDcmCtgObj="editDcmCtgObj"
			@PTselectEditDcmCtg="selectEditDcmCtg">
		</edit-text-ctgname>
		<edit-text-tags
			:db="db"
			:editDcmTagArr="editDcmTagArr"
			@PTselectEditDcmTag="selectEditDcmTag">
		</edit-text-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditDcmBody">{{ editDcmDcmObj.body }}</div>
		</div>
	</div>
	<edit-foot
		:btnClass="'dcm'"
		@PTcloseEdit="$listeners['GPcloseEditDcm']"
		@PTsaveEdit="setSaveData"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</edit-foot>
</div>
</template>

<script>
// import EditTextCtgname from './EditTextCtgname'
// import EditTextTags from './EditTextTags'
// import EditFoot from '../EditFoot'

export default {
// PT Component
	name: "AnewTextContainer",
	props: {
		db: Object,
	},
	// components: {
	// 	EditTextCtgname,
	// 	EditTextTags,
	// 	EditFoot,
	// },
	data() {
		return {
		}
	},
	methods: {
        setSaveData() {
            // this.$ 保存のためにdataを整える???
        },
	},
}
</script>