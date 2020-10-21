<template>
<ul v-if="markCtgArr.length" class="list">
	<li v-for="(ms, msidx) in markCtgArr" :key="msidx">
		<div class="cardItem">
			<i :style="{color: ms.color}"></i>
			<h2>{{ ms.head }}</h2>
			<time class="date">{{ new Date(ms.date).getFullYear() }}年{{ new Date(ms.date).getMonth()+1 }}月{{ new Date(ms.date).getDate() }}日</time>
			<p :data-cid="ms.cid">
				<button type="button" class="btnIcon def ctg edt" @click="callopenEditCtg"><span>編集</span></button>
				<button type="button" class="btnIcon def ctg viw" @click="callopenViewCtg"><span>確認</span></button>
				<button type="button" class="btnIcon def ctg rem"><span>削除</span></button>
			</p>
		</div>
	</li>
</ul>
</template>

<script>
export default {
// PT Component
	name: "PostsSeries",
	props: {
        markCtgArr: Array,
	},
	methods: {
		callopenEditCtg(e) {
			const btn = e.target,
				id = Number(btn.parentNode.dataset.cid);
			this.$emit("GPCallswitchLoader");
			this.$parent.openEditCtg(id);
		},
		callopenViewCtg(e) {
			const btn = e.target,
				id = Number(btn.parentNode.dataset.cid);
			this.$emit("GPCallswitchLoader");
			this.$emit("GPCallopenViewCtg", id);
		},
	},
}
</script>