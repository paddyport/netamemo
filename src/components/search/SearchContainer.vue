<template>
<div id="Search" class="search">
    <search-tags>
    </search-tags>
</div>
</template>

<script>
import SearchTags from './SearchTags'

export default {
// GP Component
	name: "SearchContainer",
	props: {
		db: Object,
	},
	data() {
		return {
		}
	},
	components: {
		SearchTags,
	},
	methods: {
	},
}
</script>

<style>
@import "../../css/search.css";
</style>