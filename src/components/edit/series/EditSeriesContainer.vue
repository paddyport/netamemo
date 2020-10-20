<template>
<div class="srs" :data-tid="editSrsSrsObj.sid">
    <div class="content">
		<div class="head">
			<div contenteditable="true" @blur="changeEditSrsHead">{{ editSrsSrsObj.head }}</div>
		</div>
        <edit-series-color
            :srsColor="editSrsSrsObj.color"
            @ChangeSrsSwatchClick="changeSrsColor">
        </edit-series-color>
		<edit-series-tags
			:db="db"
			:editSrsTagArr="editSrsTagArr"
			@SelectEditSrsTagArr="selectEditSrsTag">
		</edit-series-tags>
		<div class="body">
			<div contenteditable="true" @blur="changeEditSrsBody">{{ editSrsSrsObj.body }}</div>
		</div>
        <edit-series-list
            :db="db"
			:srsid="editSrsSrsObj.sid"
            :srsColor="editSrsSrsObj.color"
            :editSrsTxtArr="editSrsTxtArr"
            @CallSwitchLoader="$listeners['CallSwitchLoader']">
        </edit-series-list>
	</div>
	<edit-foot
        :btnClass="'srs'"
		@FootClickClose="$listeners['FootSrsClick']"
		@FootClickSave="setSaveData"
		@CallSwitchLoaderClick="$listeners['CallSwitchLoader']">
	</edit-foot>
</div>
</template>

<script>
import EditSeriesColor from './EditSeriesColor'
import EditSeriesTags from './EditSeriesTags'
import EditSeriesList from './EditSeriesList'
import EditFoot from '../EditFoot'

export default {
	name: "EditSeriesContainer",
	props: {
		db: Object,
		editSrsTxtArr: Array,
		editSrsSrsObj: Object,
		editSrsTagArr: Array,
	},
	components: {
        EditSeriesColor,
        EditSeriesTags,
        EditSeriesList,
        EditFoot,
	},
	data() {
		return {
            DeditSrsSrsObj: this.editSrsSrsObj,
            DeditSrsTagArr: this.editSrsTagArr,
            EeditSrsSrsColor: this.editSrsSrsObj.color,
            EeditSrsTxtArr: this.editSrsTxtArr,
            EeditSrsTagArr: this.editSrsTagArr,
		}
	},
	methods: {
		changeEditSrsHead(e) {
			const str = e.target.textContent;
			this.DeditSrsSrsObj.head = str ? str : this.DeditSrsSrsObj.head;
			e.target.textContent = this.DeditSrsSrsObj.head;
		},
		changeEditSrsBody(e) {
			const str = e.target.textContent;
			this.DeditSrsSrsObj.body = str ? str : this.DeditSrsSrsObj.body;
			e.target.textContent = this.DeditSrsSrsObj.body;
        },
        changeSrsColor(val) {
            this.EeditSrsSrsColor = val;
        },
        selectEditSrsTag(arr) {
            this.EeditSrsTagArr = arr;
        },
        setSaveData() {
			const that = this;
            // txtとtagもputする
			that.db.srs.put({
                sid: that.DeditSrsSrsObj.sid,
				tid: that.EeditSrsTxtArr,
				tag: that.EeditSrsTagArr,
				date: that.DeditSrsSrsObj.date,
				head: that.DeditSrsSrsObj.head,
                body: that.DeditSrsSrsObj.body,
                color: that.EeditSrsSrsColor,
			}).then(() => {
				that.$emit("FootSrsClick", "sav", that.DeditSrsSrsObj.sid);
				that.$emit("CallSwitchLoader");
            });
        },
	},
}
</script>