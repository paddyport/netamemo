<template>
<div class="color">
	<button type="button" :class="[!selectCtgColorFlg&&!selectCtgColor ? 'btnWord' : 'btnIcon', 'def', 'cpr', 'nml']" @click="switchCtgPalette">
		<span v-if="!selectCtgColorFlg&&!selectCtgColor"><i></i>選択</span>
		<span v-else><i></i></span>
	</button>
	<p class="code"><i v-show="selectCtgColor" :style="{background: selectCtgColor}"></i>{{ selectCtgColor }}</p>
    <color-picker
        ref="picker"
        v-show="selectCtgColorFlg"
        @EXchangeSwatch="changeCtgSwatch">
    </color-picker>
</div>
</template>

<script>
import ColorPicker from '../../parts/ColorPicker'

export default {
    name: "EditSeriesColor",
    props: {
        ctgColor: String,
    },
	components: {
        ColorPicker,
    },
	data() {
		return {
            selectCtgColorFlg: false,
            selectCtgColor: this.ctgColor,
            selectCtgPalette: "",
		}
    },
	methods: {
		switchCtgPalette() {
            this.selectCtgColorFlg = this.selectCtgColorFlg ? false : true;
            if(this.selectCtgColorFlg) this.$refs.picker.getPaletteSwatch(this.selectCtgPalette);
        },
        changeCtgSwatch(val) {
            this.selectCtgColor = val;
            this.switchCtgPalette();
            this.$emit("PTchangeCtgColor", this.selectCtgColor);
        },
	},
}
</script>