<template>
<ul v-if="markDcmArr.length" class="list">
	<li v-for="(mt, mtidx) in markDcmArr" :key="mtidx">
		<div class="cardItem">
			<i v-if="mt.color" :style="{color: mt.color}"></i>
			<h2>{{ mt.head }}</h2>
			<time class="date">{{ new Date(mt.date).getFullYear() }}年{{ new Date(mt.date).getMonth()+1 }}月{{ new Date(mt.date).getDate() }}日</time>
			<time class="last">{{ new Date(mt.last).getFullYear() }}年{{ new Date(mt.last).getMonth()+1 }}月{{ new Date(mt.last).getDate() }}日</time>
			<p :data-did="mt.did">
				<button type="button" class="btnIcon def dcm edt" @click="callopenEditDcm"><span>編集</span></button>
				<button type="button" class="btnIcon def dcm viw" @click="callopenViewDcm"><span>確認</span></button>
				<button type="button" class="btnIcon def dcm rem"><span>削除</span></button>
			</p>
		</div>
	</li>
</ul>
</template>

<script>
export default {
// PT Component
	name: "PostsText",
	props: {
        markDcmArr: Array,
	},
	methods: {
		callopenEditDcm(e) {
			const btn = e.target,
				id = btn.parentNode.dataset.did;
			this.$emit("GPCallswitchLoader");
			this.$parent.openEditDcm(id);
		},
		callopenViewDcm(e) {
			const btn = e.target,
				id = btn.parentNode.dataset.did;
			this.$emit("GPCallswitchLoader");
			this.$emit("GPCallopenViewDcm", id);
		},
	},
}
</script>