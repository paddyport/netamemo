<template>
<div class="ctg" :data-did="editCtgCtgObj.cid">
    <div class="content">
		<div class="head">
			<div contenteditable="true" @blur="changeEditCtgHead">{{ editCtgCtgObj.head }}</div>
		</div>
        <edit-series-color
            :ctgColor="editCtgCtgObj.color"
            @PTchangeCtgColor="changeCtgColor">
        </edit-series-color>
		<edit-series-tags
			:db="db"
			:editCtgTagArr="editCtgTagArr"
			@PTselectEditCtgTag="selectEditCtgTag">
		</edit-series-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditCtgBody">{{ editCtgCtgObj.body }}</div>
		</div>
        <edit-series-list
            :db="db"
			:ctgid="editCtgCtgObj.cid"
            :ctgColor="editCtgCtgObj.color"
            :editCtgDcmArr="editCtgDcmArr"
            @PTCallswitchLoader="$listeners['GPCallswitchLoader']">
        </edit-series-list>
	</div>
	<edit-foot
        :btnClass="'ctg'"
		@PTcloseEdit="$listeners['GPcloseEditCtg']"
		@PTsaveEdit="setSaveData"
		@PTCallswitchLoader="$listeners['GPCallswitchLoader']">
	</edit-foot>
</div>
</template>

<script>
import EditSeriesColor from './EditSeriesColor'
import EditSeriesTags from './EditSeriesTags'
import EditSeriesList from './EditSeriesList'
import EditFoot from '../EditFoot'

export default {
// PT Component
	name: "EditSeriesContainer",
	props: {
		db: Object,
		editCtgDcmArr: Array,
		editCtgCtgObj: Object,
		editCtgTagArr: Array,
	},
	components: {
        EditSeriesColor,
        EditSeriesTags,
        EditSeriesList,
        EditFoot,
	},
	data() {
		return {
            DeditCtgCtgObj: this.editCtgCtgObj,
            DeditCtgTagArr: this.editCtgTagArr,
			EeditCtgCtgColor: this.editCtgCtgObj.color,
			editCtgSgTagFlg: false,
		}
	},
	methods: {
		changeEditCtgHead(e) {
			const str = e.target.textContent;
			this.DeditCtgCtgObj.head = str ? str : this.DeditCtgCtgObj.head;
			e.target.textContent = this.DeditCtgCtgObj.head;
		},
		changeEditCtgBody(e) {
			const str = e.target.textContent;
			this.DeditCtgCtgObj.body = str ? str : this.DeditCtgCtgObj.body;
			e.target.textContent = this.DeditCtgCtgObj.body;
        },
        changeCtgColor(val) {
            this.EeditCtgCtgColor = val;
        },
        selectEditCtgTag(arr) {
            this.DeditCtgTagArr = arr;
        },
        setSaveData() {
			const that = this;
            // dcmもputする
			// tagもputする -> 多分できた
			that.db.ctg.put({
                cid: that.DeditCtgCtgObj.cid,
				tag: that.DeditCtgTagArr,
				date: that.DeditCtgCtgObj.date,
				head: that.DeditCtgCtgObj.head,
                body: that.DeditCtgCtgObj.body,
                color: that.EeditCtgCtgColor,
			}).then(() => {
				that.$parent.setSaveTagPromise(that.DeditCtgTagArr);
				that.$emit("GPcloseEditCtg", that.DeditCtgCtgObj.cid);
				that.$emit("GPCallswitchLoader");
            });
        },
	},
}
</script>