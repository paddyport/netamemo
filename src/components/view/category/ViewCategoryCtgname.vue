<template>
<div class="series" :data-cid="viewCtgCtgId">
	<i :style="{color: viewCtgCtgColor}"></i>
	<p>{{ viewCtgCtgName }}</p>
</div>
</template>

<script>
export default {
// CH Component
	name: "ViewCategoryCtgname",
	props: {
		viewCtgCtgId: Number,
        viewCtgCtgName: String,
        viewCtgCtgColor: String,
    },
}
</script>