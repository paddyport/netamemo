<template>
<div class="content">
    <ul v-if="postsArr.length" class="list">
        <li v-for="(ps, psidx) in postsArr" :key="psidx">
            <div class="cardItem">
                <i v-if="ps.cid" :style="{color: ps.color}"></i>
                <h2>{{ ps.head }}</h2>
                <time class="date">{{ new Date(ps.date).getFullYear() }}年{{ new Date(ps.date).getMonth()+1 }}月{{ new Date(ps.date).getDate() }}日</time>
                <time v-if="ps.did" class="last">{{ new Date(ps.last).getFullYear() }}年{{ new Date(ps.last).getMonth()+1 }}月{{ new Date(ps.last).getDate() }}日</time>
                <p :data-did="ps.did ? ps.did : 0" :data-cid="ps.did ? 0 : ps.cid">
                    <button type="button" :class="['btnIcon', 'def', ps.did ? 'dcm' : 'ctg', 'edt']" @click="edit"><span>編集</span></button>
                    <button type="button" :class="['btnIcon', 'def', ps.did ? 'dcm' : 'ctg', 'viw']" @click="view"><span>確認</span></button>
                    <button type="button" :class="['btnIcon', 'def', ps.did ? 'dcm' : 'ctg', 'rem']" @click="confirm"><span>削除</span></button>
                </p>
            </div>
        </li>
    </ul>
	<p v-else class="note">登録されていません。</p>
</div>
</template>

<script>
export default {
// PT Component
	name: "PostsContent",
	props: {
		postsArr: Array,
	},
	methods: {
        edit(e) {
            const btn = e.target,
				btnCls = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg",
				id = btnCls=="dcm" ? btn.parentNode.dataset.did : btn.parentNode.dataset.cid;
            this.$emit("GPCallshownLoader");
            switch(btnCls) {
                case "dcm":
                    this.$emit("GPCallopenEditDcm", id);
                    break;
                case "ctg":
                    this.$emit("GPCallopenEditCtg", id);
                    break;
            }
        },
        view(e) {
			const btn = e.target,
				btnCls = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg",
				id = btnCls=="dcm" ? Number(btn.parentNode.dataset.did) : Number(btn.parentNode.dataset.cid);
			this.$emit("GPCallshownLoader");
            switch(btnCls) {
                case "dcm":
                    this.$emit("GPCallopenViewDcm", id);
                    break;
                case "ctg":
                    this.$emit("GPCallopenViewCtg", id);
                    break;
            }
        },
        confirm(e) {
			const btn = e.target,
				btnCls = Number(btn.parentNode.dataset.did) ? "dcm" : "ctg";
			btn.parentNode.classList.add("isRemove");
			this.$emit("GPCallconfirmRem", btnCls);
        },
	},
}
</script>