<template>
<div id="Dialog" class="dialog">
    <div class="content">
        <p>選択したデータを削除しますか？</p>
        <p>
            <text-button
                :iconFlg="true"
                :btnStr="'削除'"
                :btnCls="btnCls+' rem'"
                :btnSize="'widMD'"
                @TextButtonClick="checkClick">
            </text-button>
            <text-button
                :iconFlg="false"
                :btnStr="'キャンセル'"
                :btnCls="'nml'"
                :btnSize="'widMD'"
                @TextButtonClick="$listeners['ANcloseDialog']">
            </text-button>
        </p>
    </div>
</div>
</template>

<script>
import TextButton from '../parts/TextButton'

export default {
// GP Component
    name: "DialogContainer",
    props: {
        btnCls: String,
    },
	components: {
        TextButton,
	},
	methods: {
		checkClick() {
            this.$emit("ANshownLoader");
			this.$emit("ANremoveItem");
		},
	},
}
</script>

<style>
@import "../../css/dialog.css";
</style>