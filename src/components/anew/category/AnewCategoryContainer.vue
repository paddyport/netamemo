<template>
<div class="ctg">
    <div class="content">
		<div class="head">
			<div contenteditable="true" data-placeholder="新規作成カテゴリ" @blur="changeAnewCtgHead"></div>
		</div>
        <anew-category-color
            @PTchangeCtgColor="changeCtgColor">
        </anew-category-color>
		<anew-category-tags
			:db="db"
			@PTselectAnewCtgTag="selectAnewCtgTag">
		</anew-category-tags>
		<div class="body">
			<div contenteditable="true" data-placeholder="新規作成コンテンツ" @blur="changeAnewCtgBody"></div>
		</div>
        <anew-category-list
            ref="list"
            :db="db"
            :ctgColor="anewCtgCtgObj.color"
            @PTselectAnewCtgDcm="selectAnewCtgDcm"
            @PTCallswitchLoader="$listeners['GPCallswitchLoader']">
        </anew-category-list>
	</div>
	<anew-foot
        :btnClass="'ctg'"
		@PTCallcloseAnew="$listeners['GPCallcloseAnew']"
		@PTsaveAnew="setSaveData"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</anew-foot>
</div>
</template>

<script>
import AnewCategoryColor from './AnewCategoryColor'
import AnewCategoryTags from './AnewCategoryTags'
import AnewCategoryList from './AnewCategoryList'
import AnewFoot from '../AnewFoot'

export default {
// PT Component
	name: "AnewCategoryContainer",
	props: {
		db: Object,
	},
	components: {
        AnewCategoryColor,
        AnewCategoryTags,
        AnewCategoryList,
        AnewFoot,
	},
	data() {
		return {
            anewCtgDcmArr: [],
            anewCtgCtgObj: {},
            anewCtgTagArr: [],
		}
	},
	methods: {
		changeAnewCtgHead(e) {
			const str = e.target.textContent;
			this.anewCtgCtgObj.head = str ? str : this.anewCtgCtgObj.head;
			e.target.textContent = this.anewCtgCtgObj.head;
		},
		changeAnewCtgBody(e) {
			const str = e.target.textContent;
			this.anewCtgCtgObj.body = str ? str : this.anewCtgCtgObj.body;
			e.target.textContent = this.anewCtgCtgObj.body;
        },
        changeCtgColor(val) {
            // this.anewCtgCtgObj.color = val;
            console.log(val, this.anewCtgCtgObj);
            this.$set(this.anewCtgCtgObj, "color", val);
            this.$refs.list.setctgColor(val);
        },
        selectAnewCtgTag(arr) {
			let _arr = arr.concat(arr);
			_arr = new Set(_arr);
            this.anewCtgTagArr = Array.from(_arr);
        },
		selectAnewCtgDcm(arr) {
			this.anewCtgDcmArr = arr;
		},
		setCngetCtgid() {
            const that = this,
                key = {head: that.anewCtgCtgObj.head};
			return new Promise(function(resolve){
				that.db.ctg.get(key).then(function(obj){
					resolve(Number(obj.cid));
				});
			});
		},
		async setAddDcm(arr) {
			const that = this,
                cid = await that.setCngetCtgid(),
                key = {cid: cid};
			return new Promise(function(resolve){
				for(let did of arr) that.db.dcm.update(did, key);
				resolve(true);
			});
		},
		async setAddremDcmPromise(arr) {
			await this.setAddDcm(arr);
		},
        setSaveData() {
            const that = this,
				_d = new Date(),
				date = _d.getFullYear()+"/"+(_d.getMonth()+1)+"/"+_d.getDate();
			that.db.ctg.add({
				tag: that.anewCtgTagArr,
				date: date,
				head: that.anewCtgCtgObj.head,
				body: that.anewCtgCtgObj.body,
				color: that.anewCtgCtgObj.color,
			}).then(() => {
				that.setAddremDcmPromise(that.anewCtgDcmArr);
				that.$parent.setSaveTagPromise(that.anewCtgTagArr);
				that.$emit("GPCallcloseAnew");
				that.$emit("GPCallswitchLoader");
			});
        },
	},
}
</script>