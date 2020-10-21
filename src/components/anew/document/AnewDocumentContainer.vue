<template>
<div class="dcm">
    <div class="content">
		<div class="head">
			<div contenteditable="true" data-placeholder="新規作成タイトル"></div>
		</div>
		<div class="body">
			<div contenteditable="true" data-placeholder="新規作成コンテンツ"></div>
		</div>
	</div>
    <anew-foot
        :btnClass="'dcm'"
        @PTcloseAnew="$listeners['GPcloseAnewDcm']"
        @PTCallswitchLoader="$listeners['GPCallswitchLoader']">
    </anew-foot>
</div>
</template>

<script>
// import EditTextCtgname from './EditTextCtgname'
// import EditTextTags from './EditTextTags'
import AnewFoot from '../AnewFoot'

export default {
// PT Component
	name: "AnewDocumentContainer",
	props: {
		db: Object,
	},
	components: {
	// 	EditTextCtgname,
	// 	EditTextTags,
		AnewFoot,
	},
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