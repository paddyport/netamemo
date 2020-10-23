<template>
<div id="App" :class="[deviceType]">
	<calendar-container
		ref="calendar"
		:db="db"
		:currentYYMM="{year: currentYear, month: currentMonth}"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		:changeMonthFlg="changeMonthFlg"
		@ANsetCalendarData="setCalendarData"
		@ANsetChangeMonth="setChangeMonth"
		@ANsetCurrent="setCurrent"
		@ANswitchChangeMonth="switchChangeMonth"
		@ANopenPosts="openPosts"
		@ANopenAnewDcm="openAnewDcm"
		@ANopenAnewCtg="openAnewCtg"
		@ANswitchLoader="switchLoader">
	</calendar-container>
	<menu-container
		ref="menu"
		:db="db"
		:menuListBtnFlg="menuListBtnFlg"
		:menuListFlg="menuListFlg"
		@ANopenViewDcm="openViewDcm"
		@ANopenPosts="openPosts"
		@ANswitchMenuList="switchMenuList"
		@ANswitchLoader="switchLoader">
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
		@ANopenArchive="openArchive">
	</search-container>
	<archive-container
		:archiveFlg="archiveFlg"
		:archiveHead="archiveHead"
		:archiveArr="archiveArr"
		@ANswitchLoader="switchLoader">
	</archive-container>
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
import ArchiveContainer from "./components/archive/ArchiveContainer"

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
			changeMonthList: {},
			changeMonthBtnFlg: false,
			changeMonthFlg: false,
			menuListBtnFlg: true,
			menuListFlg: false,
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
		ArchiveContainer,
	},
	created: function(){
		this.checkDevice();
		this.setToday();
		this.createDB();
		this.createTable();
		this.markDate = this.now.getDate();
		// this.addDataDcm();
		// this.addDataCtg();
		// this.addDataTag();
		this.switchLoader();
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
		switchLoader() {
			this.loaderFlg = this.loaderFlg ? false : true;
			console.log(this.loaderFlg);
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
			if(!flg) {
				this.$refs.calendar.setCalendar(yymm);
				this.switchChangeMonth();
			}
		},
		setCalendarData(arr) {
			this.currentDates = arr;
		},
		setChangeMonth(obj) {
			this.changeMonthList = Object.assign(obj);
			this.changeMonthBtnFlg = !Object.keys(this.changeMonthList).length ? false : true;
		},
		switchChangeMonth() {
			this.menuListBtnFlg = this.changeMonthFlg ? true : false;
			this.changeMonthFlg = this.changeMonthFlg ? false : true;
		},
		switchMenuList() {
			this.changeMonthBtnFlg = this.menuListFlg&&Object.keys(this.changeMonthList).length  ? true : false;
			this.menuListFlg = this.menuListFlg ? false : true;
		},
		openPosts(arr, str, date) {
			this.postsDate = date;
			this.postsHead = str;
			this.$refs.posts.setPosts(arr);
			this.postsFlg = true;
			this.switchLoader();
		},
		closePosts() {
			this.postsFlg = false;
		},
		openViewDcm(id) {
			this.$refs.view.setDcmData(id);
			this.viewFlg = true;
			this.switchLoader();
		},
		openViewCtg(id) {
			this.$refs.view.setCtgData(id);
			this.viewFlg = true;
			this.switchLoader();
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
			if(this.postsFlg) this.$refs.calendar.setPostsData(this.postsDate);
			if(id && this.viewFlg) this.$refs.view.setDcmData(Number(id));
			this.$refs.calendar.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.$refs.menu.setMenuData();
			this.switchLoader();
		},
		closeEditCtg(id) {
			this.editFlg = false;
			if(this.postsFlg) this.$refs.calendar.setPostsData(this.postsDate);
			if(id && this.viewFlg) this.$refs.view.setCtgData(Number(id), true);
			this.$refs.calendar.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.$refs.menu.setMenuData();
			this.switchLoader();
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
			this.$refs.calendar.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.$refs.menu.setMenuData();
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
		async removeItem() {
			const btn = document.getElementsByClassName("isRemove")[0],
				item = btn.dataset.did ? "dcm" : "ctg";
			if(item=="dcm") {
				await this.removeDcm(Number(btn.dataset.did));
			} else {
				await this.setRemoveCtg(Number(btn.dataset.cid));
			}
			if(this.postsFlg) this.$refs.calendar.setPostsData(this.postsDate);
			if(this.viewFlg) this.closeView();
			this.switchDialog();
			this.$refs.calendar.setCalendar({year: this.currentYear, month: this.currentMonth});
			this.$refs.menu.setMenuData();
			this.switchLoader();
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
		async setRemoveCtg(id) {
			const that = this;
			await that.removeCtg({cid: id});
			return new Promise(function(resolve){
				that.db.ctg.delete(id).then(() => {
					resolve(true);
				});
			});
		},
		openArchive(arr, str) {
			this.archiveArr = this.archiveArr.concat(arr);
			this.archiveArr = this.compileArrtoArr(this.archiveArr);
			this.archiveHead = str;
			this.archiveFlg = true;
			this.switchLoader();
		},
	},
	computed: {
	},
});
</script>

<style>
@import "./css/app.css";
</style>
