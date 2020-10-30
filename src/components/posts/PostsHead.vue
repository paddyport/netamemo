<template>
<div class="head"><h1>{{ postsHead }}</h1></div>
</template>

<script>
export default {
// PT Component
	name: "PostsHead",
	props: {
        postsHead: String,
	},
}
</script>