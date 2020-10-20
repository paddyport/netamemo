<template>
<div class="content">
	<div class="head"><div>{{ viewSrsHead }}</div></div>
	<div class="body"><div>{{ viewSrsBody }}</div></div>
	<div class="list">
		<ul v-if="viewSrsTxtArr.length">
			<li v-for="(vt, vtidx) in viewSrsTxtArr" :key="vtidx">
				<div class="cardItem">
					<i :style="{color: viewSrsColor}"></i>
					<h2>{{ vt.head }}</h2>
					<time class="date">{{ new Date(vt.date).getFullYear() }}年{{ new Date(vt.date).getMonth()+1 }}月{{ new Date(vt.date).getDate() }}日</time>
					<time class="last">{{ new Date(vt.last).getFullYear() }}年{{ new Date(vt.last).getMonth()+1 }}月{{ new Date(vt.last).getDate() }}日</time>
					<p :data-tid="vt.tid">
						<button type="button" class="btnIcon def txt viw" @click="onClickView"><span>確認</span></button>
					</p>
				</div>
			</li>
		</ul>
		<p v-else>登録されていません。</p>
	</div>
</div>
</template>

<script>
export default {
	name: "ViewSeriesContent",
	props: {
        viewSrsHead: String,
		viewSrsBody: String,
		viewSrsTxtArr: Array,
		viewSrsColor: String,
    },
	methods: {
        onClickView(e) {
			const btn = e.target,
				id = btn.parentNode.getAttribute("data-tid");
				console.log(id);
            this.$emit("ItemClickView", id);
        },
	},
}
</script>