<template>
<div class="tag">
	<p :class="viewSrsTagFlg ? 'isShown' : ''">
		<span v-for="(vtg, vtgidx) in viewSrsTagArr" :data-tag="vtg" :key="vtgidx">{{ vtg }}</span>
	</p>
	<button type="button" class="icn tag" @click="switchViewTags">箋</button>
</div>
</template>

<script>
export default {
	name: "ViewTxtTags",
	props: {
        viewSrsTagArr: Array,
    },
    data() {
        return {
            viewSrsTagFlg: false,
        }
    },
    methods: {
        switchViewTags() {
            this.viewSrsTagFlg = this.viewSrsTagFlg ? false : true;
        },
    },
}
</script>