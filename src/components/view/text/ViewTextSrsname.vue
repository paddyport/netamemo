<template>
<div class="series" :data-sid="viewTxtSrsId">
	<i :style="{color: viewTxtSrsColor}"></i>
	<p>{{ viewTxtSrsName }}</p>
</div>
</template>

<script>
export default {
	name: "ViewTextSrsname",
	props: {
		viewTxtSrsId: Number,
        viewTxtSrsName: String,
        viewTxtSrsColor: String,
    },
}
</script>