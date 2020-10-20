<template>
		<ul v-if="markSrsArr.length" class="list">
			<li v-for="(ms, msidx) in markSrsArr" :key="msidx">
				<div class="cardItem">
					<i :style="{color: ms['color']}"></i>
					<h2>{{ ms["head"] }}</h2>
					<time class="date">{{ new Date(ms["date"]).getFullYear() }}年{{ new Date(ms["date"]).getMonth()+1 }}月{{ new Date(ms["date"]).getDate() }}日</time>
					<p :data-sid="ms['sid']">
							<button type="button" class="btnIcon def srs edt"><span>編集</span></button>
							<button type="button" class="btnIcon def srs viw" @click="openViewSrs"><span>確認</span></button>
							<button type="button" class="btnIcon def srs rem"><span>削除</span></button>
					</p>
				</div>
			</li>
		</ul>
</template>

<script>
export default {
	name: "PostsSeries",
	props: {
        markSrsArr: Array,
	},
	methods: {
		openViewSrs(e) {
			const btn = e.target,
				id = btn.parentNode.dataset.sid;
			this.$emit("PostsSrsView", id);
		},
	},
}
</script>