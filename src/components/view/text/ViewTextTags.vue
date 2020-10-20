<template>
<div class="tag">
	<p :class="viewTxtTagFlg ? 'isShown' : ''">
		<span v-for="(vtg, vtgidx) in viewTxtTagArr" :data-tag="vtg" :key="vtgidx">{{ vtg }}</span>
	</p>
	<button type="button" class="icn tag" @click="switchViewTags">箋</button>
</div>
</template>

<script>
export default {
	name: "ViewTxtTags",
	props: {
        viewTxtTagArr: Array,
    },
    data() {
        return {
            viewTxtTagFlg: false,
        }
    },
    methods: {
        switchViewTags() {
            this.viewTxtTagFlg = this.viewTxtTagFlg ? false : true;
        },
    },
}
</script>