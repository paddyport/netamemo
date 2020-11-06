<template>
<div class="foot">
    <text-button
        :iconFlg="false"
        :btnStr="'キャンセル'"
        :btnCls="'nml'"
        :btnSize="'widLG'"
        :btnFlg="true"
        @TextButtonClick="$listeners['PTCallcloseAnew']">
    </text-button>
    <text-button
        :iconFlg="true"
        :btnStr="'保存'"
        :btnCls="btnCls"
        :btnSize="'widLG'"
        :btnFlg="AsaveFlg"
        @TextButtonClick="clickSave">
    </text-button>
</div>
</template>

<script>
import TextButton from '../parts/TextButton'

export default {
// CH Component
    name: "AnewFoot",
    props: {
        btnCls: String,
        AsaveFlg: Boolean,
    },
	components: {
        TextButton,
	},
	methods: {
        clickSave() {
			this.$emit("PTCallshownLoader");
            this.$emit("PTsaveAnew");
        },
	},
}
</script>