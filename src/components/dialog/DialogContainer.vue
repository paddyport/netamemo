<template>
<div id="Dialog" class="dialog">
    <div class="content">
        <p>選択したデータを削除しますか？</p>
        <p>
            <dialog-button
                :btnType="true"
                :btnStr="'削除'"
                :btnCls="btnCls"
                @GPCallremoveItem="$listeners['ANremoveItem']"
                @GPCallshownLoader="$listeners['ANshownLoader']">
            </dialog-button>
            <dialog-button
                :btnType="false"
                :btnStr="'キャンセル'"
                :btnCls="'nml'"
                @GPCallcloseDialog="$listeners['ANcloseDialog']">
            </dialog-button>
        </p>
    </div>
</div>
</template>

<script>
import DialogButton from './DialogButton'

export default {
// GP Component
    name: "DialogContainer",
    props: {
        btnCls: String,
    },
	components: {
		DialogButton,
	},
}
</script>

<style>
@import "../../css/dialog.css";
</style>