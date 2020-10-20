<template>
<div id="Calendar" class="calendar">
	<calendar-head
		:currentYYMM="currentYYMM"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		@HeadFlg="$listeners['CalendarHeadFlg']"
		@HeadClick="$listeners['CalendarHeadClick']">
	</calendar-head>
	<calendar-change-month
		:changeMonthFlg="changeMonthFlg"
		:changeMonthList="changeMonthList">
	</calendar-change-month>
	<calendar-body
		:currentDates="currentDates"
		:currentFirstDay="currentFirstDay"
		@BodyClick="$listeners['CalendarBodyClick']"></calendar-body>
	<calendar-foot @FootClick="$listeners['CalendarFootClick']"></calendar-foot>
</div>
</template>

<script>
import CalendarHead from './CalendarHead'
import CalendarChangeMonth from './CalendarChangeMonth'
import CalendarBody from './CalendarBody'
import CalendarFoot from './CalendarFoot'

export default {
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
			changeMonthList: {},
		}
	},
	components: {
		CalendarHead,
		CalendarChangeMonth,
		CalendarBody,
		CalendarFoot,
	},
	created: function(){
		this.setCalendar();
	},
	methods: {
		setCalendar() {
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
						"txt": [],
						"srs": [],
						"dtid": [],
						"ltid": [],
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
			that.db.txt.toArray().then((list) => {
				for(let _data of list) {
					const _d = new Date(_data.date),
						_date = _d.getTime(),
						_l = new Date(_data.last),
						_last = _l.getTime(),
						dy = _d.getFullYear(),
						dm = _d.getMonth(),
						dym = dy+"-"+dm;
					if(_cstime<=_date && _date<_cetime) {
						let _i = _d.getDate()+that.currentFirstDay-1;
						that.currentDates[_i].txt.push(_data.tid);
						that.currentDates[_i].txt = that.$parent.compileArrtoArr(that.currentDates[_i].txt);
						that.currentDates[_i].dtid.push(_data.tid);
						that.currentDates[_i].dtid = that.$parent.compileArrtoArr(that.currentDates[_i].dtid);
					}
					if(_cstime<=_last && _last<_cetime) {
						let _i = _l.getDate()+that.currentFirstDay-1;
						that.currentDates[_i].txt.push(_data.tid);
						that.currentDates[_i].txt = that.$parent.compileArrtoArr(that.currentDates[_i].txt);
						that.currentDates[_i].ltid.push(_data.tid);
						that.currentDates[_i].ltid = that.$parent.compileArrtoArr(that.currentDates[_i].ltid);
					}
					if(that.changeMonthList[dym]) {
						if(that.changeMonthList[dym].tid) {
							that.changeMonthList[dym].tid.push(_data.tid);
							that.changeMonthList[dym].tid = that.$parent.compileArrtoArr(that.changeMonthList[dym].tid);
						} else {
							that.changeMonthList[dym] = {tid: [_data.tid]};
							if(!that.changeMonthList[dym].sid) {
								that.changeMonthList[dym] = {sid: []};
							}
						}
					} else {
						that.changeMonthList = {
							[dym] : {
								tid: [_data.tid],
								sid: []
							}
						};
					}
				}
				that.$emit("tableDates", that.currentDates);
				that.$emit("changeMonthData", that.changeMonthList);
			});
			that.db.srs.toArray().then((list) => {
				for(let _data of list) {
					const _d = new Date(_data.date),
						_date = _d.getTime(),
						dy = _d.getFullYear(),
						dm = _d.getMonth(),
						dym = dy+"-"+dm;
					if(_cstime<=_date && _date<_cetime) {
						let _i = _d.getDate()+that.currentFirstDay-1;
						if(that.currentDates[_i].srs.indexOf(_data.sid)<0) {
							that.currentDates[_i].srs.push(_data.sid);
						}
					}
					if(that.changeMonthList[dym]) {
						if(that.changeMonthList[dym].sid) {
							that.changeMonthList[dym].sid.push(_data.sid);
							that.changeMonthList[dym].sid = that.$parent.compileArrtoArr(that.changeMonthList[dym].sid);
						} else {
							that.changeMonthList[dym] = {sid: [_data.sid]};
							if(!that.changeMonthList[dym].tid) {
								that.changeMonthList[dym] = {tid: []};
							}
						}
					} else {
						that.changeMonthList = {
							[dym] : {
								sid: [_data.sid],
								tid: []
							}
						};
					}
				}
				that.$emit("tableData", that.currentDates);
				that.$emit("changeMonthData", that.changeMonthList);
			});
		},
	},
}
</script>

<style>
@import "../../css/calendar.css";
</style>