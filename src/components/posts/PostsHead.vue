<template>
<div class="head">
	<h1>{{ currentYYMM.year }}年{{ currentYYMM.month+1 }}月{{ markDate }}日</h1>
</div>
</template>

<script>
export default {
	name: "PostsHead",
	props: {
        currentYYMM: Object,
        markDate: Number,
	},
}
</script>