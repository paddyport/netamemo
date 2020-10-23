<template>
<div id="Calendar" class="calendar">
	<calendar-head
		:currentYYMM="currentYYMM"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		@GPCallswitchChangeMonth="$listeners['ANswitchChangeMonth']">
	</calendar-head>
	<calendar-change
		:currentYYMM="currentYYMM"
		:changeMonthFlg="changeMonthFlg"
		:changeMonthObj="changeMonthObj"
		@GPCallsetCurrent="$listeners['ANsetCurrent']">
	</calendar-change>
	<calendar-body
		:currentDates="currentDates"
		:currentFirstDay="currentFirstDay"
		@GPsetPostsData="setPostsData"
		@GPCallswitchLoader="$listeners['ANswitchLoader']">
	</calendar-body>
	<footer class="footer">
		<calendar-foot-button
			:btnStr="'新規作成'"
			:btnCls="'dcm'"
			@GPCallopenAnewDcm="$listeners['ANopenAnewDcm']">
		</calendar-foot-button>
		<calendar-foot-button
			:btnStr="'新規作成'"
			:btnCls="'ctg'"
			@GPCallopenAnewCtg="$listeners['ANopenAnewCtg']">
		</calendar-foot-button>
	</footer>
</div>
</template>

<script>
import CalendarHead from './CalendarHead'
import CalendarChange from './CalendarChange'
import CalendarBody from './CalendarBody'
import CalendarFootButton from './CalendarFootButton'

export default {
// GP Component
	name: "CalendarContainer",
	props: {
		db: Object,
		currentYYMM: Object,
		changeMonthBtnFlg: Boolean,
		changeMonthFlg: Boolean,
	},
	data() {
		return {
			weekLen: 7,
			currentFirstDay: 0,
			currentDatesCnt: 0,
			currentWeeks: 0,
			currentDates: [],
			changeMonthObj: {},
			setpostsArr: [],
		}
	},
	components: {
		CalendarHead,
		CalendarChange,
		CalendarBody,
		CalendarFootButton,
	},
	created: function(){
		this.setCalendar(this.currentYYMM);
	},
	methods: {
		setCalendar(yymm) {
			this.changeMonthObj = {};
			this.currentFirstDay = new Date(yymm.year, yymm.month).getDay();
			this.currentDatesCnt = this.$parent.getCurrentDates(yymm.year, yymm.month);
			this.currentWeeks = Math.ceil((this.currentFirstDay+this.currentDatesCnt)/7);
			this.currentDates = this.setCalendarArr(yymm);
			this.setDataCtg(yymm);
		},
		setCalendarArr(yymm) {
			let result = [];
			for(let w=0;w<this.currentWeeks;w++) {
				for(let d=0;d<this.weekLen;d++) {
					let obj,
						_d = !w&&d<this.currentFirstDay ? null : w*this.weekLen+(d+1)-this.currentFirstDay,
						_t = new Date(yymm.year, yymm.month, _d).getTime();
					_d = _d&&this.currentDatesCnt<_d ? null : _d;
					obj = {
						"date": _d,
						"number": !_d ? null : this.$parent.zeroPad(_d, 2),
						"time": _t,
						"dcm": [],
						"ctg": [],
						"ddid": [],
					};
					result.push(obj);
				}
			}
			return result;
		},
		setDataDcm(cs, ce) {
			const that = this;
			return new Promise(function(resolve){
				that.db.dcm.toArray().then((list) => {
					for(let obj of list) {
						const time = obj.date,
							dy = new Date(time).getFullYear(),
							dm = new Date(time).getMonth()+1,
							dym = dy+"-"+dm;
						if(cs<=time && time<ce) {
							let _i = new Date(time).getDate()+that.currentFirstDay-1;
							that.currentDates[_i].dcm.push(obj.did);
							that.currentDates[_i].dcm = that.$parent.compileArrtoArr(that.currentDates[_i].dcm);
							that.currentDates[_i].ddid.push(obj.did);
							that.currentDates[_i].ddid = that.$parent.compileArrtoArr(that.currentDates[_i].ddid);
						}
						if(!that.changeMonthObj[dym]) {
							that.$set(that.changeMonthObj, dym, {did: [], cid: []});
						}
						that.changeMonthObj[dym].did.push(obj.did);
						that.changeMonthObj[dym].did = that.$parent.compileArrtoArr(that.changeMonthObj[dym].did);
					}
					resolve(true);
				});
			});
		},
		async setDataCtg(yymm) {
			const that = this,
				cstime = new Date(yymm.year, yymm.month).getTime(),
				cetime = new Date(yymm.year, yymm.month+1).getTime();
			await this.setDataDcm(cstime, cetime);
			that.db.ctg.toArray().then((list) => {
				for(let obj of list) {
					const time = obj.date,
						dy = new Date(time).getFullYear(),
						dm = new Date(time).getMonth()+1,
						dym = dy+"-"+dm;
					if(cstime<=time && time<cetime) {
						let _i = new Date(time).getDate()+that.currentFirstDay-1;
						if(that.currentDates[_i].ctg.indexOf(obj.cid)<0) {
							that.currentDates[_i].ctg.push(obj.cid);
						}
					}
					if(!that.changeMonthObj[dym]) {
						that.$set(that.changeMonthObj, dym, {did: [], cid: []});
					}
					that.changeMonthObj[dym].cid.push(obj.cid);
					that.changeMonthObj[dym].cid = that.$parent.compileArrtoArr(that.changeMonthObj[dym].cid);
				}
				that.$emit("ANsetCalendarData", that.currentDates);
				that.$emit("ANsetChangeMonth", that.changeMonthObj);
			});
		},
		getCtgData(ct) {
			let one = [],
				all = {};
			const that = this;
			return new Promise(function(resolve){
				that.db.ctg.toArray().then(function(list){
					for(let obj of list) {
						if(obj.date==ct) one.push(obj);
						that.$set(all, obj.cid, obj);
					}
					resolve({one: one, all: all});
				});
			});
		},
		async setPostsData(date) {
			let arr = [];
			const that = this,
				head = `${this.currentYYMM.year}年${(this.currentYYMM.month+1)}月${date}日`,
				ct = new Date(this.currentYYMM.year, this.currentYYMM.month, date).getTime(),
				ctgArr = await this.getCtgData(ct);
			that.db.dcm.where({date: ct}).toArray().then(function(list){
				for(let obj of list) {
					const c = obj.cid&&ctgArr["all"][obj.cid].color ? ctgArr["all"][obj.cid].color : "";
					that.$set(obj, "color", c);
					arr.push(obj);
				}
				that.setpostsArr = arr.concat(ctgArr.one);
				that.setpostsArr = that.$parent.compileArrtoArr(that.setpostsArr);
				that.$emit("ANopenPosts", that.setpostsArr, head, date);
			});
		},
	},
}
</script>

<style>
@import "../../css/calendar.css";
</style>