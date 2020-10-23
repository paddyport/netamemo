<template>
<div v-if="archiveFlg" id="Archive" class="archive">
    <div class="content">
        <ul v-if="sortArchiveArr.length" class="list">
            <li v-for="(sa, saidx) in sortArchiveArr" :key="saidx">
                <div class="cardItem">
                    <i v-if="sa.cid" :style="{color: sa.color}"></i>
                    <h2>{{ sa.head }}</h2>
                    <time class="date">{{ new Date(sa.date).getFullYear() }}年{{ new Date(sa.date).getMonth()+1 }}月{{ new Date(sa.date).getDate() }}日</time>
                    <time v-if="sa.did" class="last">{{ new Date(sa.last).getFullYear() }}年{{ new Date(sa.last).getMonth()+1 }}月{{ new Date(sa.last).getDate() }}日</time>
                    <p :data-did="sa.did ? sa.did : 0" :data-cid="sa.did ? 0 : sa.cid">
                        <button type="button" :class="['btnIcon', 'def', 'dcm', 'edt']" @click="callopenEditDcm"><span>編集</span></button>
                        <button type="button" class="btnIcon def dcm viw" @click="callopenViewDcm"><span>確認</span></button>
                        <button type="button" class="btnIcon def dcm rem" @click="callconfirmRem"><span>削除</span></button>
                    </p>
                </div>
            </li>
        </ul>
        <p v-else class="note">登録されていません。</p>
    </div>
    <div class="head">
        <h1>{{ archiveHead }}</h1>
    </div>
	<button type="button" class="icn" @click="callclosePosts">消</button>
</div>
</template>

<script>
export default {
// GP Component
	name: "ArchiveContainer",
	props: {
        archiveFlg: Boolean,
        archiveHead: String,
        archiveArr: Array,
	},
	data() {
		return {
            sortArchiveArr: this.archiveArr,
		}
	},
	methods: {
	},
}
</script>

<style>
@import "../../css/archive.css";
</style>