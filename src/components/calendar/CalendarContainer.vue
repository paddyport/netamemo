<template>
<div id="Calendar" class="calendar">
	<calendar-head
		:currentYYMM="currentYYMM"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		@GPCallswitchChangeMonth="$listeners['ANswitchChangeMonth']">
	</calendar-head>
	<calendar-change
		:changeMonthFlg="changeMonthFlg"
		:changeMonthObj="changeMonthObj">
	</calendar-change>
	<calendar-body
		:currentDates="currentDates"
		:currentFirstDay="currentFirstDay"
		@GPCallopenPosts="$listeners['ANopenPosts']"
		@GPCallswitchLoader="$listeners['ANswitchLoader']"></calendar-body>
	<calendar-foot
		@GPCallopenAnewDcm="$listeners['ANopenAnewDcm']"
		@GPCallopenAnewCtg="$listeners['ANopenAnewCtg']">
	</calendar-foot>
</div>
</template>

<script>
import CalendarHead from './CalendarHead'
import CalendarChange from './CalendarChange'
import CalendarBody from './CalendarBody'
import CalendarFoot from './CalendarFoot'

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
			dayArray: ["sun", "mon", "tue", "wed", "thu", "fri", "sat"],
			currentFirstDay: 0,
			currentDatesCnt: 0,
			currentWeeks: 0,
			currentDates: [],
			changeMonthObj: {},
		}
	},
	components: {
		CalendarHead,
		CalendarChange,
		CalendarBody,
		CalendarFoot,
	},
	created: function(){
		this.setCalendar();
	},
	methods: {
		setCalendar() {
			this.changeMonthObj = {};
			this.currentFirstDay = new Date(this.currentYYMM.year, this.currentYYMM.month).getDay();
			this.currentDatesCnt = this.$parent.getCurrentDates(this.currentYYMM.year, this.currentYYMM.month);
			this.currentWeeks = Math.ceil((this.currentFirstDay+this.currentDatesCnt)/7);
			this.currentDates = this.setCalendarArr();
			this.setCalendarData();
		},
		setCalendarArr() {
			let result = [];
			for(let w=0;w<this.currentWeeks;w++) {
				for(let d=0;d<this.dayArray.length;d++) {
					let obj,
						_d = !w&&d<this.currentFirstDay ? null : w*this.dayArray.length+(d+1)-this.currentFirstDay,
						_t = new Date(this.currentYYMM.year, this.currentYYMM.month, _d).getTime();
					_d = _d&&this.currentDatesCnt<_d ? null : _d;
					obj = {
						"date": _d,
						"number": !_d ? null : this.$parent.zeroPad(_d, 2),
						"time": _t,
						"dcm": [],
						"ctg": [],
						"ddid": [],
						// "ldid": [],
					};
					result.push(obj);
				}
			}
			return result;
		},
		setCalendarData() {
			let that = this,
				_cstime = new Date(that.currentYYMM.year, that.currentYYMM.month).getTime(),
				_cetime = new Date(that.currentYYMM.year, that.currentYYMM.month+1).getTime();
			that.db.dcm.toArray().then((list) => {
				for(let _data of list) {
					const _d = new Date(_data.date),
						_date = _d.getTime(),
						dy = _d.getFullYear(),
						dm = _d.getMonth(),
						dym = dy+"-"+dm;
					if(_cstime<=_date && _date<_cetime) {
						let _i = _d.getDate()+that.currentFirstDay-1;
						that.currentDates[_i].dcm.push(_data.did);
						that.currentDates[_i].dcm = that.$parent.compileArrtoArr(that.currentDates[_i].dcm);
						that.currentDates[_i].ddid.push(_data.did);
						that.currentDates[_i].ddid = that.$parent.compileArrtoArr(that.currentDates[_i].ddid);
					}
					if(that.changeMonthObj[dym]) {
						if(that.changeMonthObj[dym].did) {
							that.changeMonthObj[dym].did.push(_data.did);
							that.changeMonthObj[dym].did = that.$parent.compileArrtoArr(that.changeMonthObj[dym].did);
						} else {
							that.changeMonthObj[dym] = {did: [_data.did]};
							if(!that.changeMonthObj[dym].cid) {
								that.changeMonthObj[dym] = {cid: []};
							}
						}
					} else {
						that.changeMonthObj = {
							[dym] : {
								did: [_data.did],
								cid: []
							}
						};
					}
				}
				that.$emit("ANsetCalendarData", that.currentDates);
				that.$emit("ANsetChangeMonth", that.changeMonthObj);
			});
			that.db.ctg.toArray().then((list) => {
				for(let _data of list) {
					const _d = new Date(_data.date),
						_date = _d.getTime(),
						dy = _d.getFullYear(),
						dm = _d.getMonth(),
						dym = dy+"-"+dm;
					if(_cstime<=_date && _date<_cetime) {
						let _i = _d.getDate()+that.currentFirstDay-1;
						if(that.currentDates[_i].ctg.indexOf(_data.cid)<0) {
							that.currentDates[_i].ctg.push(_data.cid);
						}
					}
					if(that.changeMonthObj[dym]) {
						if(that.changeMonthObj[dym].cid) {
							that.changeMonthObj[dym].cid.push(_data.cid);
							that.changeMonthObj[dym].cid = that.$parent.compileArrtoArr(that.changeMonthObj[dym].cid);
						} else {
							that.changeMonthObj[dym] = {cid: [_data.cid]};
							if(!that.changeMonthObj[dym].did) {
								that.changeMonthObj[dym] = {did: []};
							}
						}
					} else {
						that.changeMonthObj = {
							[dym] : {
								cid: [_data.cid],
								did: []
							}
						};
					}
				}
				that.$emit("ANsetCalendarData", that.currentDates);
				that.$emit("ANsetChangeMonth", that.changeMonthObj);
			});
		},
	},
}
</script>

<style>
@import "../../css/calendar.css";
</style>