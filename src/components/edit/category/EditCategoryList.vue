<template>
<div class="list">
	<ul>
		<li v-for="(art, artidx) in addremCtgDcmArr" v-show="!(art.cid&&art.cid!=ctgid)" :key="artidx">
			<div class="cardItem">
				<i v-if="art.color" :style="{color: art.color}"></i>
				<h2>{{ art.head }}</h2>
				<time class="date">{{ new Date(art.date).getFullYear() }}年{{ new Date(art.date).getMonth()+1 }}月{{ new Date(art.date).getDate() }}日</time>
				<time class="last">{{ new Date(art.last).getFullYear() }}年{{ new Date(art.last).getMonth()+1 }}月{{ new Date(art.last).getDate() }}日</time>
				<p :data-did="art.did">
					<button type="button" :class="['btnIcon', 'def', 'nml', 'pls', defaultDcmArr.indexOf(art.did)>-1 ? 'isNoActive' : '']" @click="addDcmArr"><span>増加</span></button>
					<button type="button" :class="['btnIcon', 'def', 'nml', 'mns', defaultDcmArr.indexOf(art.did)<0 ? 'isNoActive' : '']" @click="remDcmArr"><span>削減</span></button>
				</p>
			</div>
		</li>
	</ul>
</div>
</template>

<script>
export default {
// CH Component
	name: "EditCategoryList",
	props: {
		db: Object,
		ctgid: Number,
		ctgColor: String,
		editCtgDcmArr: Array,
	},
	data() {
		return {
            DeditCtgDcmArr: this.editCtgDcmArr,
            defaultDcmArr: [],
			addremCtgDcmArr: [],
		}
	},
	created: function(){
        this.$emit("PTCallswitchLoader");
        this.setDcmArr();
        this.getDataDcmPromise();
	},
	methods: {
        setDcmArr() {
            for(let obj of this.editCtgDcmArr) {
                this.defaultDcmArr.push(obj.did);
            }
        },
		getDataDcm() {
            const that = this;
            return new Promise(function(resolve){
                that.db.dcm.toArray().then((list) => {
                    that.addremCtgDcmArr = list;
                    resolve(list);
                });
            });
		},
		async getDataDcmPromise() {
			const that = this;
            await that.getDataDcm();
            for(let idx in that.addremCtgDcmArr) {
                const cid = that.addremCtgDcmArr[idx].cid;
                if(cid) {
                    that.db.ctg.get({cid: cid}).then((obj) => {
                        that.$set(that.addremCtgDcmArr[idx], "color", obj.color);
                    });
                }
            }
            this.$emit("PTCallswitchLoader");
		},
		addDcmArr(e) {
			const that = this,
				btn = e.target,
				id = Number(btn.parentNode.dataset.did);
			that.db.dcm.get(id).then((obj) => {
				that.DeditCtgDcmArr.push(obj);
			});
			for(let idx in that.addremCtgDcmArr) {
				const did = that.addremCtgDcmArr[idx].did;
				if(did==id) {
					that.defaultDcmArr.push(did);
					that.$set(that.addremCtgDcmArr[idx], "color", that.ctgColor);
				}
			}
			that.$emit("PTSelectEditCtgDcm", that.defaultDcmArr);
		},
		remDcmArr(e) {
			const btn = e.target,
				id = Number(btn.parentNode.dataset.did);
			for(let idx in this.DeditCtgDcmArr) {
				const did = this.DeditCtgDcmArr[idx].did;
				if(did==id) this.DeditCtgDcmArr.splice(idx, 1);
			}
			for(let idx in this.addremCtgDcmArr) {
				const did = this.addremCtgDcmArr[idx].did;
				if(did==id) this.$set(this.addremCtgDcmArr[idx], "color", "");
			}
			this.defaultDcmArr.splice(this.defaultDcmArr.indexOf(id), 1);
			this.$emit("PTSelectEditCtgDcm", this.defaultDcmArr);
		},
	},
}
</script>