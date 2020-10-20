<template>
<div class="series" :data-sid="viewSrsSrsId">
	<i :style="{color: viewSrsSrsColor}"></i>
	<p>{{ viewSrsSrsName }}</p>
</div>
</template>

<script>
export default {
	name: "ViewSeriesSrsname",
	props: {
		viewSrsSrsId: Number,
        viewSrsSrsName: String,
        viewSrsSrsColor: String,
    },
}
</script>