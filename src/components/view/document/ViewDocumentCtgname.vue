<template>
<div class="series" :data-cid="viewDcmCtgId">
	<i :style="{color: viewDcmCtgColor}"></i>
	<p>{{ viewDcmCtgName }}</p>
</div>
</template>

<script>
export default {
// CH Component
	name: "ViewDocumentCtgname",
	props: {
		viewDcmCtgId: Number,
        viewDcmCtgName: String,
        viewDcmCtgColor: String,
    },
}
</script>