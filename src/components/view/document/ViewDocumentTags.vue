<template>
<div class="tag">
	<p :class="tagFlg ? 'isShown' : ''">
		<span v-for="(vtg, vtgidx) in viewDcmTagArr" :data-tag="vtg" :key="vtgidx">{{ vtg }}</span>
	</p>
	<button type="button" class="icn tag" @click="switchTag">箋</button>
</div>
</template>

<script>
export default {
// CH Component
	name: "ViewDocumentTags",
	props: {
        viewDcmTagArr: Array,
    },
    data() {
        return {
            tagFlg: false,
        }
    },
    methods: {
        switchTag() {
            this.tagFlg = this.tagFlg ? false : true;
        },
    },
}
</script>