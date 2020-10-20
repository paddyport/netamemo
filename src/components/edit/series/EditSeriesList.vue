<template>
<div class="list">
	<ul>
		<li v-for="(art, artidx) in addremSrsTxtArr" v-show="!(art.sid&&art.sid!=srsid)" :key="artidx">
			<div class="cardItem">
				<i v-if="art.color" :style="{color: art.color}"></i>
				<h2>{{ art.head }}</h2>
				<time class="date">{{ new Date(art.date).getFullYear() }}年{{ new Date(art.date).getMonth()+1 }}月{{ new Date(art.date).getDate() }}日</time>
				<time class="last">{{ new Date(art.last).getFullYear() }}年{{ new Date(art.last).getMonth()+1 }}月{{ new Date(art.last).getDate() }}日</time>
				<p :data-tid="art.tid">
					<button type="button" :class="['btnIcon', 'def', 'nml', 'pls', defaultTxtArr.indexOf(art.tid)>-1 ? 'isNoActive' : '']" @click="addTxtArr"><span>増加</span></button>
					<button type="button" :class="['btnIcon', 'def', 'nml', 'mns', defaultTxtArr.indexOf(art.tid)<0 ? 'isNoActive' : '']"><span>削減</span></button>
				</p>
			</div>
		</li>
	</ul>
</div>
</template>

<script>
export default {
	name: "EditSeriesList",
	props: {
		db: Object,
		srsid: Number,
		srsColor: String,
		editSrsTxtArr: Array,
	},
	data() {
		return {
            DeditSrsTxtArr: this.editSrsTxtArr,
            defaultTxtArr: [],
			addremSrsTxtArr: [],
		}
	},
	created: function(){
        this.$emit("CallSwitchLoader");
        this.setTxtArr();
        this.getDataTxtPromise();
	},
	methods: {
        setTxtArr() {
            for(let obj of this.editSrsTxtArr) {
                this.defaultTxtArr.push(obj.tid);
            }
        },
		getDataTxt() {
            const that = this;
            return new Promise(function(resolve){
                that.db.txt.toArray().then((list) => {
                    that.addremSrsTxtArr = list;
                    resolve(list);
                });
            });
		},
		async getDataTxtPromise() {
			const that = this;
            await that.getDataTxt();
            for(let idx in that.addremSrsTxtArr) {
                const sid = that.addremSrsTxtArr[idx].sid;
                if(sid) {
                    that.db.srs.get({sid: sid}).then((obj) => {
                        that.$set(that.addremSrsTxtArr[idx], "color", obj.color);
                    });
                }
            }
            this.$emit("CallSwitchLoader");
		},
		addTxtArr(e) {
			const that = this,
				btn = e.target,
				id = btn.parentNode.getAttribute("data-tid");
			that.db.txt.get(id).then((obj) => {
				that.DeditSrsTxtArr.push(obj);
			});
			for(let idx in that.addremSrsTxtArr) {
				const tid = that.addremSrsTxtArr[idx].tid;
				if(tid==id) {
					that.defaultTxtArr.push(tid);
					that.$set(that.addremSrsTxtArr[idx], "color", that.srsColor);
					console.log(that.addremSrsTxtArr[idx]);
				}
			}
		},
	},
}
</script>