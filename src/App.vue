<template>
<div id="App" :class="[deviceType]">
	<calendar-container
		:currentYYMM="{year: currentYear, month: currentMonth}"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		:changeMonthFlg="changeMonthFlg"
		:changeMonthObj="changeMonthObj"
		:currentDates="currentDates"
		@ANsetChangeMonth="setChangeMonth"
		@ANsetCurrent="setCurrent"
		@ANswitchChangeMonth="switchChangeMonth"
		@ANsetPostsData="setPostsData"
		@ANopenPosts="openPosts"
		@ANopenAnewDcm="openAnewDcm"
		@ANopenAnewCtg="openAnewCtg"
		@ANswitchLoader="switchLoader">
	</calendar-container>
	<menu-container
		:menuListBtnFlg="menuListBtnFlg"
		:menuListFlg="menuListFlg"
		:dcmNewArr="dcmNewArr"
		:dcmEdtArr="dcmEdtArr"
		@ANaccordionNext="accordionNext"
		@ANopenViewDcm="openViewDcm"
		@ANopenPostsDcmAllData="openPostsDcmAllData"
		@ANopenPostsCtgAllData="openPostsCtgAllData"
		@ANopenSearchTags="openSearchTags"
		@ANswitchMenuList="switchMenuList"
		@ANshownLoader="shownLoader">
	</menu-container>
	<posts-container
		ref="posts"
		:db="db"
		:currentYYMM="{year: currentYear, month: currentMonth}"
		:postsFlg="postsFlg"
		:postsHead="postsHead"
		@ANclosePosts="closePosts"
		@ANopenEditDcm="openEditDcm"
		@ANopenEditCtg="openEditCtg"
		@ANopenViewDcm="openViewDcm"
		@ANopenViewCtg="openViewCtg"
		@ANconfirmRem="confirmRem"
		@ANswitchLoader="switchLoader">
	</posts-container>
	<view-container
		ref="view"
		:db="db"
		:viewFlg="viewFlg"
		@ANopenViewDcm="openViewDcm"
		@ANopenEditDcm="openEditDcm"
		@ANopenEditCtg="openEditCtg"
		@ANconfirmRem="confirmRem"
		@ANcloseView="closeView"
		@ANswitchLoader="switchLoader">
	</view-container>
	<edit-container
		ref="edit"
		:db="db"
		:editFlg="editFlg"
		@ANcloseEditDcm="closeEditDcm"
		@ANcloseEditCtg="closeEditCtg"
		@ANswitchLoader="switchLoader">
	</edit-container>
	<anew-container
		ref="anew"
		:db="db"
		:anewFlg="anewFlg"
		@ANcloseAnew="closeAnew"
		@ANswitchLoader="switchLoader">
	</anew-container>
	<search-container
		:db="db"
		@ANopenPosts="openPosts">
	</search-container>
	<dialog-container
		v-if="dialogFlg"
		:btnClass="dialogBtnClass"
		@ANcloseDialog="closeDialog"
		@ANremoveItem="removeItem"
		@ANswitchLoader="switchLoader">
	</dialog-container>
	<loader-container v-if="loaderFlg"></loader-container>
</div>
</template>

<script>
import Vue from "vue"
import Dexie from "dexie"
import LoaderContainer from "./components/loader/LoaderContainer"
import DialogContainer from "./components/dialog/DialogContainer"
import CalendarContainer from "./components/calendar/CalendarContainer"
import MenuContainer from "./components/menu/MenuContainer"
import PostsContainer from "./components/posts/PostsContainer"
import ViewContainer from "./components/view/ViewContainer"
import EditContainer from "./components/edit/EditContainer"
import AnewContainer from "./components/anew/AnewContainer"
import SearchContainer from "./components/search/SearchContainer"

export default Vue.extend({
// AN Component
	name: "App",
	data() {
		return {
			loaderFlg: true,
			dialogFlg: false,
			dialogBtnClass: "",
			dbName: "netamemoDB",
			db: "",
			dbData: [],
			now: "",
			nowTime: 0,
			current: "",
			currentYear: 0,
			currentMonth: 0,
			currentDates: [],
			weekLen: 7,
			currentFirstDay: 0,
			currentDatesCnt: 0,
			currentWeeks: 0,
			changeMonthObj: {},
			setpostsArr: [],
			changeMonthList: {},
			changeMonthBtnFlg: false,
			changeMonthFlg: false,
			menuListBtnFlg: true,
			menuListFlg: false,
			dcmNewArr: [],
			dcmEdtArr: [],
			markDate: 0,
			postsFlg: false,
			postsDate: 0,
			postsHead: "",
			viewFlg: false,
			editFlg: false,
			currentField: "",
			anewFlg: false,
			archiveFlg: false,
			archiveHead: "",
			archiveArr: [],
		}
	},
	components: {
		LoaderContainer,
		DialogContainer,
		CalendarContainer,
		MenuContainer,
		PostsContainer,
		ViewContainer,
		EditContainer,
		AnewContainer,
		SearchContainer,
	},
	created: function(){
		this.checkDevice();
		this.createDB();
		this.createTable();
		this.setToday();
		this.setMenuData();
		this.markDate = this.now.getDate();
		// this.addDataDcm();
		// this.addDataCtg();
		// this.addDataTag();
		this.hiddenLoader();
	},
	methods: {
		checkDevice() {
			const ua = navigator.userAgent.toLowerCase();
			if(ua.indexOf("iphone")>0 || ua.indexOf("ipod")>0 || ua.indexOf("android")>0 && ua.indexOf("mobile")>0) {
				this.deviceType = "isTD";
			} else if (ua.indexOf("ipad")>0 || ua.indexOf("android")>0){
				this.deviceType = "isTD";
			} else {
				this.deviceType = "isMD";
			}
		},
		compileArrtoArr(arr) {
			let _arr = new Set(arr);
			return Array.from(_arr);
		},
		compileNltoBr(_str) {
			let str = String(_str);
			return str.replace(/<div>/, "\r\n").replace(/<\/div>/, "");
		},
		compileBrtoNl(_str) {
			let str = String(_str);
			return str.replace(/<br>/, "\r\n");
		},
		zeroPad(num, len) {
			return ('0000'+num).slice(-len);
		},
		getCurrentDates(y, m) {
			return new Date(parseInt(y, 10), parseInt(m+1, 10), 0).getDate();
		},
		sortASC(prop) {
			return function(a, b){
				if(a[prop]<b[prop]) return -1;
				if(a[prop]>b[prop]) return 1;
				return 0;
			};
		},
		sortDESC(prop) {
			return function(a, b){
				if(a[prop]>b[prop]) return -1;
				if(a[prop]<b[prop]) return 1;
				return 0;
			};
		},
		accordionNext(e) {
			const btn = e.target,
				acc = btn.nextElementSibling;
			if(btn.classList.contains("isOpen")) {
				acc.removeAttribute("style");
				btn.classList.remove("isOpen")
			} else {
				const h = acc.children.length*btn.clientHeight;
				acc.style.height = h+"px";
				btn.classList.add("isOpen")
			}
		},
		switchLoader() {
			this.loaderFlg = this.loaderFlg ? false : true;
			console.log("s", this.loaderFlg);
		},
		shownLoader() {
			this.loaderFlg = true;
			console.log(this.loaderFlg);
		},
		hiddenLoader() {
			this.loaderFlg = false;
			console.log(this.loaderFlg);
		},
		createDB() {
			this.db = new Dexie(this.dbName);
		},
		createTable() {
			this.db.version(1).stores({
				dcm: `++did, cid, tag, date, last, head, body`,
				ctg: `++cid, tag, date, color, head, body`,
				tag: `++gid, head`,
			});
			// did: num, cid: num(null=0), tag: arr, date: num(ms), last: num(ms), head: str, body: str
			// cid: num, tag: arr, date: num(ms), color: #***, head: str, body: str
			// gid: num, head: str
		},
		addDataDcm() {
			this.db.dcm.put({cid: 2, tag: ["硯"], date: new Date(2020,8,25).getTime(), last: new Date(2020,8,30).getTime(), head: "徒然なるまゝに", body: "つれづれなるまゝに、日暮らし、硯にむかひて、心にうつりゆくよしなし事を、そこはかとなく書きつくれば、あやしうこそものぐるほしけれ。つれづれなるまゝに、日暮らし、硯にむかひて、心にうつりゆくよしなし事を、そこはかとなく書きつくれば、あやしうこそものぐるほしけれ。"});
			this.db.dcm.put({cid: 0, tag: ["日暮らし"], date: new Date(2020,9,10).getTime(), last: new Date(2020,9,25).getTime(), head: "徒然なるまゝに", body: "つれづれなるまゝに、日暮らし、硯にむかひて、心にうつりゆくよしなし事を、そこはかとなく書きつくれば、あやしうこそものぐるほしけれ。つれづれなるまゝに、日暮らし、硯にむかひて、心にうつりゆくよしなし事を、そこはかとなく書きつくれば、あやしうこそものぐるほしけれ。"});
			this.db.dcm.put({cid: 1, tag: ["徒然", "日暮らし"], date: new Date(2020,9,20).getTime(), last: new Date(2020,9,22).getTime(), head: "日暮らし", body: "いでや、この世に生れては、願はしかるべき事こそ多かンめれ。<br>御門(みかど)の御位は、いともかしこし。竹の園生(そのふ)の、末葉(すゑば)まで人間の種ならぬぞ、やんごとなき。一(いち)の人の御有様はさらなり、たゞ人(びと)も、舎人(とねり)など給はるきはは、ゆゝしと見ゆ。その子・孫(むまご)までは、はふれにたれど、なほなまめかし。それより下(しも)つかたは、ほどにつけつゝ、時にあひ、したり顔なるも、みづからはいみじと思ふらめど、いとくちをし。"});
			this.db.dcm.put({cid: 2, tag: [], date: new Date(2020,9,30).getTime(), last: new Date(2020,9,30).getTime(), head: "硯にむかひて", body: "因幡国(いなばのくに)に、何の入道とかやいふ者の娘、かたちよしと聞きて、人あまた言ひわたりけれども、この娘、たゞ、栗をのみ食ひて、更に、米(よね)の類を食はざりれば、「かゝる異様(ことやう)の者、人に見ゆべきにあらず」とて、親許さざりけり。"});
			this.db.dcm.put({cid: 2, tag: ["硯"], date: new Date(2020,9,1).getTime(), last: new Date(2020,9,11).getTime(), head: "因幡国", body: "因幡国(いなばのくに)に、何の入道とかやいふ者の娘、かたちよしと聞きて、人あまた言ひわたりけれども、この娘、たゞ、栗をのみ食ひて、更に、米(よね)の類を食はざりれば、「かゝる異様(ことやう)の者、人に見ゆべきにあらず」とて、親許さざりけり。"});
		},
		addDataCtg() {
			this.db.ctg.put({tag: ["徒然", "日暮らし"], date: new Date(2020,9,22).getTime(), color: "#fdcfdb", head: "心にうつりゆく", body: "公世(きんよ)の二位にゐのせうとに、良覚僧正(りやうがくそうじやう)と聞えしは、極(きは)めて腹あしき人なりけり。"});
			this.db.ctg.put({tag: ["硯"], date: new Date(2020,9,10).getTime(), color: "#145c8a", head: "よしなし事を", body: "能(のう)をつかんとする人、「よくせざらんほどは、なまじひに人に知(し)られじ。うちうちよく習ひ得(え)て、さし出(い)でたらんこそ、いと心にくからめ」と常に言ふめれど、かく言ふ人、一芸も習(なら)ひ得(う)ることなし。"});
		},
		addDataTag() {
			this.db.tag.put({head: "日暮らし"});
			this.db.tag.put({head: "徒然"});
			this.db.tag.put({head: "硯"});
		},
		getDcmAllData() {
			const that = this;
			return new Promise(function(resolve){
				that.db.dcm.toArray().then((list) => {
					resolve(list);
				});
			});
		},
		getSetCtgData(ct) {
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
		getCtgAllData() {
			const that = this;
			return new Promise(function(resolve){
				that.db.ctg.toArray().then((list) => {
					resolve(list);
				});
			});
		},
		getTagAllData() {
			const that = this;
			return new Promise(function(resolve){
				that.db.tag.toArray().then((list) => {
					resolve(list);
				});
			});
		},
		setToday() {
			this.now = new Date();
			this.now = new Date(this.now.getFullYear(), this.now.getMonth(), this.now.getDate());
			this.nowTime = this.now.getTime();
			this.current = new Date(this.now.getFullYear(), this.now.getMonth());
			this.setCurrent({year: this.now.getFullYear(), month: this.now.getMonth()});
		},
		setCurrent(yymm) {
			let pt = this.current.getTime(),
				ct = new Date(yymm.year, yymm.month).getTime(),
				flg = Boolean(pt==ct);
			this.current = new Date(yymm.year, yymm.month);
			this.currentYear = this.current.getFullYear();
			this.currentMonth = this.current.getMonth();
			this.setCalendar(yymm);
			if(!flg) {
				this.switchChangeMonth();
			}
		},
		setCalendar(yymm) {
			this.changeMonthObj = {};
			this.currentFirstDay = new Date(yymm.year, yymm.month).getDay();
			this.currentDatesCnt = this.getCurrentDates(yymm.year, yymm.month);
			this.currentWeeks = Math.ceil((this.currentFirstDay+this.currentDatesCnt)/7);
			this.setCalendarArr(yymm);
			this.setDataCtg(yymm);
		},
		setCalendarArr(yymm) {
			this.currentDates = [];
			for(let w=0;w<this.currentWeeks;w++) {
				for(let d=0;d<this.weekLen;d++) {
					let obj,
						_d = !w&&d<this.currentFirstDay ? null : w*this.weekLen+(d+1)-this.currentFirstDay,
						_t = new Date(yymm.year, yymm.month, _d).getTime();
					_d = _d&&this.currentDatesCnt<_d ? null : _d;
					obj = {
						"date": _d,
						"number": !_d ? null : this.zeroPad(_d, 2),
						"time": _t,
						"dcm": [],
						"ctg": [],
					};
					this.currentDates.push(obj);
				}
			}
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
							that.currentDates[_i].dcm = that.compileArrtoArr(that.currentDates[_i].dcm);
						}
						if(!that.changeMonthObj[dym]) {
							that.$set(that.changeMonthObj, dym, {did: [], cid: []});
						}
						that.changeMonthObj[dym].did.push(obj.did);
						that.changeMonthObj[dym].did = that.compileArrtoArr(that.changeMonthObj[dym].did);
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
					that.changeMonthObj[dym].cid = that.compileArrtoArr(that.changeMonthObj[dym].cid);
				}
				that.setChangeMonth(that.changeMonthObj);
			});
		},
		setChangeMonth(obj) {
			this.changeMonthList = Object.assign(obj);
			this.changeMonthBtnFlg = !Object.keys(this.changeMonthList).length ? false : true;
		},
		setMenuData() {
			const that = this;
			that.dcmNewArr = [];
			that.dcmEdtArr = [];
			that.db.dcm.toArray().then((list) => {
				for(let _data of list) {
                    let obj = _data;
                    obj.date = _data.date;
                    obj.last = _data.last;
					that.dcmNewArr.push(obj);
					that.dcmNewArr.sort(that.sortDESC("date"));
					that.dcmEdtArr.push(obj);
					that.dcmEdtArr.sort(that.sortDESC("last"));
				}
				that.dcmNewArr.slice(0, 4);
				that.dcmEdtArr.slice(0, 4);
			});
		},
		switchChangeMonth() {
			this.menuListBtnFlg = this.changeMonthFlg ? true : false;
			this.changeMonthFlg = this.changeMonthFlg ? false : true;
		},
		switchMenuList() {
			this.changeMonthBtnFlg = this.menuListFlg&&Object.keys(this.changeMonthList).length  ? true : false;
			this.menuListFlg = this.menuListFlg ? false : true;
		},
		async setPostsData(date) {
			let arr = [];
			const that = this,
				head = `${this.currentYear}年${(this.currentMonth+1)}月${date}日`,
				ct = new Date(this.currentYear, this.currentMonth, date).getTime(),
				ctgArr = await this.getSetCtgData(ct);
			that.db.dcm.where({date: ct}).toArray().then(function(list){
				for(let obj of list) {
					const c = obj.cid&&ctgArr["all"][obj.cid].color ? ctgArr["all"][obj.cid].color : "";
					that.$set(obj, "color", c);
					arr.push(obj);
				}
				that.setpostsArr = arr.concat(ctgArr.one);
				that.setpostsArr = that.compileArrtoArr(that.setpostsArr);
				that.openPosts(that.setpostsArr, head, date);
			});
		},
		async openPostsDcmAllData(str) {
			const ctgAllData = await this.getCtgAllData(),
				dcmAllData = await this.getDcmAllData(),
				cidArr = ctgAllData.map(function(val) {
					return val.cid;
				});
			for(let obj of dcmAllData) {
				const cid = obj.cid ? obj.cid : false,
					col = cid ? ctgAllData[cidArr.indexOf(cid)].color : "";
				this.$set(obj, "color", col);
			}
			this.openPosts(dcmAllData, str);
		},
		async openPostsCtgAllData(str) {
			const ctgAllData = await this.getCtgAllData();
			this.openPosts(ctgAllData, str);
		},
		openPosts(arr, str, date) {
			this.postsDate = date ? date : this.postsDate;
			this.postsHead = str;
			this.$refs.posts.setPosts(arr);
			this.postsFlg = true;
			this.hiddenLoader();
		},
		closePosts() {
			this.postsFlg = false;
		},
		async openSearchTags(str) {
			const tagAllData = await this.getTagAllData();
			this.openSearch(tagAllData, str);
		},
		openSearch(arr, str) {
			console.log(arr, str);
			this.hiddenLoader();
		},
		openViewDcm(id) {
			this.$refs.view.setDcmData(id);
			this.viewFlg = true;
			this.hiddenLoader();
		},
		openViewCtg(id) {
			this.$refs.view.setCtgData(id);
			this.viewFlg = true;
			this.hiddenLoader();
		},
		closeView() {
			this.viewFlg = this.viewFlg ? false : true;
		},
		openEditDcm(obj) {
			this.$refs.edit.setEditDcmData(obj);
			this.editFlg = true;
		},
		openEditCtg(obj) {
			this.$refs.edit.setEditCtgData(obj);
			this.editFlg = true;
		},
		closeEditDcm(id) {
			this.editFlg = false;
			if(this.postsFlg) this.setPostsData(this.postsDate);
			if(id && this.viewFlg) this.$refs.view.setDcmData(Number(id));
			this.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.setMenuData();
			this.hiddenLoader();
		},
		closeEditCtg(id) {
			this.editFlg = false;
			if(this.postsFlg) this.setPostsData(this.postsDate);
			if(id && this.viewFlg) this.$refs.view.setCtgData(Number(id), true);
			this.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.setMenuData();
			this.hiddenLoader();
		},
		openAnewDcm() {
			this.$refs.anew.setAnew("dcm");
			this.anewFlg = true;
		},
		openAnewCtg() {
			this.$refs.anew.setAnew("ctg");
			this.anewFlg = true;
		},
		closeAnew() {
			this.anewFlg = false;
			this.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.setMenuData();
		},
		switchDialog() {
			this.dialogFlg = this.dialogFlg ? false : true;
		},
		closeDialog() {
			const btn = document.getElementsByClassName("isRemove")[0];
			btn.classList.remove("isRemove");
			this.dialogBtnClass = "";
			this.switchDialog();
		},
		confirmRem(val) {
			this.switchDialog();
			this.dialogBtnClass = val;
		},
		removeDcm(id) {
			const that = this;
			return new Promise(function(resolve){
				that.db.dcm.delete(id).then(() => {
					resolve(true);
				});
			});
		},
		removeCtg(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.dcm.where(key).modify({cid: 0}).then(function(){
					resolve(true);
				});
			});
		},
		async removeItem() {
			const btn = document.getElementsByClassName("isRemove")[0],
				item = btn.dataset.did ? "dcm" : "ctg";
			if(item=="dcm") {
				await this.removeDcm(Number(btn.dataset.did));
			} else {
				await this.setRemoveCtg(Number(btn.dataset.cid));
			}
			if(this.postsFlg) this.setPostsData(this.postsDate);
			if(this.viewFlg) this.closeView();
			this.switchDialog();
			this.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.setMenuData();
			this.hiddenLoader();
		},
		async setRemoveCtg(id) {
			const that = this;
			await that.removeCtg({cid: id});
			return new Promise(function(resolve){
				that.db.ctg.delete(id).then(() => {
					resolve(true);
				});
			});
		},
	},
});
</script>

<style>
@import "./css/app.css";
</style>
