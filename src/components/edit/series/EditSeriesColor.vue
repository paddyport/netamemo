<template>
<div class="color">
	<button type="button" :class="[!selectSrsColorFlg&&!selectSrsColor ? 'btnWord' : 'btnIcon', 'def', 'cpr', 'nml']" @click="switchSrsPalette">
		<span v-if="!selectSrsColorFlg&&!selectSrsColor"><i></i>選択</span>
		<span v-else><i></i></span>
	</button>
	<p class="code"><i v-show="selectSrsColor" :style="{background: selectSrsColor}"></i>{{ selectSrsColor }}</p>
    <colorpicker-container
        v-show="selectSrsColorFlg"
        @ChangeSwatch="changeSrsSwatch">
    </colorpicker-container>
</div>
</template>

<script>
import ColorpickerContainer from '../../ColorpickerContainer'

export default {
    name: "EditSeriesColor",
    props: {
        srsColor: String,
    },
	components: {
        ColorpickerContainer,
    },
	data() {
		return {
            selectSrsColorFlg: false,
            selectSrsColor: this.srsColor,
		}
    },
	methods: {
		switchSrsPalette() {
			this.selectSrsColorFlg = this.selectSrsColorFlg ? false : true;
        },
        changeSrsSwatch(val) {
            this.selectSrsColor = val;
            this.$emit("ChangeSrsSwatchClick", this.selectSrsColor);
        },
	},
}
</script>