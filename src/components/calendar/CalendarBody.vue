<template>
<div class="body">
	<div class="month">
		<div v-for="(cd, cdidx) in currentDates" class="day" :key="cdidx">
			<a v-if="cd['txt'].length || cd['srs'].length" class="content" :data-date="cd['date']" @click="onBodyClick">
				<p v-if="cd['txt'].length" class="txt">文</p>
				<p v-if="cd['srs'].length" class="srs">連</p>
			</a>
			<div v-if="cd['date']" class="index">{{ cd["number"] }}</div>
		</div>
	</div>
</div>
</template>

<script>
export default {
	name: 'CalendarBody',
	props: {
		currentDates: Array,
		currentFirstDay: Number,
	},
	methods: {
		onBodyClick(e) {
			const btn = e.target,
				date = Number(btn.dataset.date),
				idx = date-1+this.currentFirstDay;
			this.$emit("BodyClick", {date: date, idx: idx});
		},
	},
}
</script>