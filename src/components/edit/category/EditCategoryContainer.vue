<template>
<div class="ctg" :data-did="editCtgCtgObj.cid">
    <div class="content">
		<div class="head">
			<div contenteditable="true" @blur="changeEditCtgHead">{{ editCtgCtgObj.head }}</div>
		</div>
        <edit-category-color
            :ctgColor="editCtgCtgObj.color"
            @PTchangeCtgColor="changeCtgColor">
        </edit-category-color>
		<edit-category-tags
			:db="db"
			:editCtgTagArr="editCtgTagArr"
			@PTselectEditCtgTag="selectEditCtgTag">
		</edit-category-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditCtgBody">{{ editCtgCtgObj.body }}</div>
		</div>
        <edit-category-list
            :db="db"
			:ctgid="editCtgCtgObj.cid"
            :ctgColor="editCtgCtgObj.color"
            :editCtgDcmArr="editCtgDcmArr"
			@PTSelectEditCtgDcm="selectEditCtgDcm"
            @PTCallswitchLoader="$listeners['GPCallswitchLoader']">
        </edit-category-list>
	</div>
	<edit-foot
        :btnClass="'ctg'"
		:saveFlg="saveFlg"
		@PTcloseEdit="$listeners['GPcloseEditCtg']"
		@PTsaveEdit="setSaveData"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</edit-foot>
</div>
</template>

<script>
import EditCategoryColor from './EditCategoryColor'
import EditCategoryTags from './EditCategoryTags'
import EditCategoryList from './EditCategoryList'
import EditFoot from '../EditFoot'

export default {
// PT Component
	name: "EditCategoryContainer",
	props: {
		db: Object,
		editCtgDcmArr: Array,
		editCtgCtgObj: Object,
		editCtgTagArr: Array,
	},
	components: {
        EditCategoryColor,
        EditCategoryTags,
        EditCategoryList,
        EditFoot,
	},
	data() {
		return {
			DeditCtgDcmArr: [],
            DeditCtgCtgObj: this.editCtgCtgObj,
            DeditCtgTagArr: this.editCtgTagArr,
			EeditCtgCtgColor: this.editCtgCtgObj.color,
			saveFlg: true,
		}
	},
	created: function(){
		this.changeEditCtgDcm(this.editCtgDcmArr);
	},
	methods: {
		compileNltoBr(_str) {
			let str = String(_str);
			return str.replace(/<div>/, "\r\n").replace(/<\/div>/, "");
		},
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		changeEditCtgHead(e) {
			const str = e.target.innerText;
			this.saveFlg = !str||!str.match(/\S/g) ? false : true;
			this.DeditCtgCtgObj.head = !str||!str.match(/\S/g) ? "" : this.compileNltoBr(str);
			e.target.innerText = this.compileBrtoNl(this.DeditCtgCtgObj.head);
		},
		changeEditCtgBody(e) {
			const str = e.target.innerText;
			this.DeditCtgCtgObj.body = !str||!str.match(/\S/g) ? "" : this.compileNltoBr(str);
			e.target.innerText = this.compileBrtoNl(this.DeditCtgCtgObj.body);
        },
        changeCtgColor(val) {
            this.EeditCtgCtgColor = val;
        },
        selectEditCtgTag(arr) {
			let _arr = arr.concat(arr);
			_arr = new Set(_arr);
            this.DeditCtgTagArr = Array.from(_arr);
		},
		changeEditCtgDcm(arr) {
			for(let obj of arr) this.DeditCtgDcmArr.push(obj.did);
		},
		selectEditCtgDcm(arr) {
			this.DeditCtgDcmArr = arr;
		},
		setRemDcm(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.dcm.where(key).modify({cid: 0}).then(function(){
					resolve(true);
				});
			});
		},
		async setAddDcm(arr) {
			const that = this,
				key = {cid: that.DeditCtgCtgObj.cid};
			await that.setRemDcm(key);
			return new Promise(function(resolve){
				for(let did of arr) that.db.dcm.update(did, key);
				resolve(true);
			});
		},
		async setAddremDcmPromise(arr) {
			await this.setAddDcm(arr);
		},
        setSaveData() {
			const that = this;
			that.db.ctg.update(that.DeditCtgCtgObj.cid, {
				tag: that.DeditCtgTagArr,
				date: that.DeditCtgCtgObj.date,
				head: that.DeditCtgCtgObj.head,
                body: that.DeditCtgCtgObj.body,
                color: that.EeditCtgCtgColor,
			}).then(() => {
				that.setAddremDcmPromise(this.DeditCtgDcmArr);
				that.$parent.setSaveTagPromise(that.DeditCtgTagArr);
				that.$emit("GPcloseEditCtg", that.DeditCtgCtgObj.cid);
				that.$emit("GPCallswitchLoader");
			});
        },
	},
}
</script>