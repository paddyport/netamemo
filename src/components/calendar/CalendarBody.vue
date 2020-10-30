<template>
<div class="body">
	<div class="month">
		<div v-for="(cd, cdidx) in currentDates" class="day" :key="cdidx">
			<a v-if="cd.dcm.length || cd.ctg.length" class="content" :data-date="cd.date" @click="setPostsData">
				<p v-if="cd.dcm.length" class="dcm">記</p>
				<p v-if="cd.ctg.length" class="ctg">類</p>
			</a>
			<div v-if="cd.date" class="index">{{ cd.number }}</div>
		</div>
	</div>
</div>
</template>

<script>
export default {
// PT Component
	name: 'CalendarBody',
	props: {
		currentDates: Array,
	},
	methods: {
		setPostsData(e) {
			const btn = e.target,
				date = Number(btn.dataset.date);
			this.$emit("GPCallswitchLoader");
			this.$emit("GPCallsetPostsData", date);
		},
	},
}
</script>