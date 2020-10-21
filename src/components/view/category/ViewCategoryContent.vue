<template>
<div class="content">
	<div class="head"><div>{{ viewCtgHead }}</div></div>
	<div class="body"><div>{{ viewCtgBody }}</div></div>
	<div class="list">
		<ul v-if="viewCtgDcmArr.length">
			<li v-for="(vt, vtidx) in viewCtgDcmArr" :key="vtidx">
				<div class="cardItem">
					<i :style="{color: viewCtgColor}"></i>
					<h2>{{ vt.head }}</h2>
					<time class="date">{{ new Date(vt.date).getFullYear() }}年{{ new Date(vt.date).getMonth()+1 }}月{{ new Date(vt.date).getDate() }}日</time>
					<time class="last">{{ new Date(vt.last).getFullYear() }}年{{ new Date(vt.last).getMonth()+1 }}月{{ new Date(vt.last).getDate() }}日</time>
					<p :data-did="vt.did">
						<button type="button" class="btnIcon def dcm viw" @click="callopenViewDcm"><span>確認</span></button>
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
// CH Component
	name: "ViewCategoryContent",
	props: {
        viewCtgHead: String,
		viewCtgBody: String,
		viewCtgDcmArr: Array,
		viewCtgColor: String,
    },
	methods: {
        callopenViewDcm(e) {
			const btn = e.target,
				id = Number(btn.parentNode.getAttribute("data-did"));
            this.$emit("PTCallopenViewDcm", id);
        },
	},
}
</script>