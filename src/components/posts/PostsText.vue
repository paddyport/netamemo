<template>
<ul v-if="markTxtArr.length" class="list">
	<li v-for="(mt, mtidx) in markTxtArr" :key="mtidx">
		<div class="cardItem">
			<i v-if="mt.color" :style="{color: mt.color}"></i>
			<h2>{{ mt.head }}</h2>
			<time class="date">{{ new Date(mt.date).getFullYear() }}年{{ new Date(mt.date).getMonth()+1 }}月{{ new Date(mt.date).getDate() }}日</time>
			<time class="last">{{ new Date(mt.last).getFullYear() }}年{{ new Date(mt.last).getMonth()+1 }}月{{ new Date(mt.last).getDate() }}日</time>
			<p :data-tid="mt.tid">
				<button type="button" class="btnIcon def txt edt" @click="openEditTxt"><span>編集</span></button>
				<button type="button" class="btnIcon def txt viw" @click="openViewTxt"><span>確認</span></button>
				<button type="button" class="btnIcon def txt rem"><span>削除</span></button>
			</p>
		</div>
	</li>
</ul>
</template>

<script>
export default {
	name: "PostsText",
	props: {
        markTxtArr: Array,
	},
	methods: {
		openEditTxt(e) {
			const btn = e.target,
				id = btn.parentNode.dataset.tid;
			this.$emit("PostsTxtEdit", id);
		},
		openViewTxt(e) {
			const btn = e.target,
				id = btn.parentNode.dataset.tid;
			this.$emit("PostsTxtView", id);
		},
	},
}
</script>