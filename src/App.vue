<template>
<div id="App" :class="[deviceType]">
	<calendar-container 
		:db="db"
		:currentYYMM="{year: currentYear, month: currentMonth}"
		:changeMonthBtnFlg="changeMonthBtnFlg"
		:changeMonthFlg="changeMonthFlg"
		@tableData="setCalendarData"
		@changeMonthData="setChangeMonth"
		@CalendarHeadFlg="switchChangeMonth"
		@CalendarHeadClick="switchChangeMonth"
		@CalendarBodyClick="openPosts"
		@CalendarFootClick="openAnewField">
	</calendar-container>
	<menu-container
		:db="db"
		:menuListBtnFlg="menuListBtnFlg"
		:menuListFlg="menuListFlg"
		@MenuBtnClick="switchMenuList">
	</menu-container>
	<posts-container
		ref="posts"
		:db="db"
		:currentYYMM="{year: currentYear, month: currentMonth}"
		:postsFlg="postsFlg"
		@CallCompileBrtoNl="compileBrtoNl"
		@PostsCloseClick="closePosts"
		@EditTxtMark="openEditTxtMark"
		@EditSrsMark="openEditSrsMark"
		@ViewTxtMark="openViewTxtMark"
		@ViewSrsMark="openViewSrsMark"
		@GetDataSrsPromise="getDataSrsPromise">
	</posts-container>
	<view-container
		ref="view"
		:db="db"
		:viewFlg="viewFlg"
		@ViewMarkText="openViewTxtMark"
		@EditMarkText="openEditTxtMark"
		@EditMarkSeries="openEditSrsMark"
		@switchViewClick="switchView">
	</view-container>
	<edit-container
		ref="edit"
		:db="db"
		:editFlg="editFlg"
		@CloseEditClick="closeEdit"
		@SwitchLoader="switchLoader">
	</edit-container>
	<loader-container v-if="loaderFlg"></loader-container>
</div>
</template>

<script>
import Vue from "vue"
import Dexie from "dexie"
import LoaderContainer from "./components/LoaderContainer"
import CalendarContainer from "./components/calendar/CalendarContainer"
import MenuContainer from "./components/menu/MenuContainer"
import PostsContainer from "./components/posts/PostsContainer"
import ViewContainer from "./components/view/ViewContainer"
import EditContainer from "./components/edit/EditContainer"

export default Vue.extend({
	name: "App",
	props: {
	},
	data() {
		return {
			loaderFlg: true,
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
			viewFlg: false,
			editFlg: false,
			currentField: "",
			currentEdit: "",
			anewFieldFlg: false,
		}
	},
	components: {
		LoaderContainer,
		CalendarContainer,
		MenuContainer,
		PostsContainer,
		ViewContainer,
		EditContainer,
	},
	created: function(){
		this.checkDevice();
		this.setToday();
		this.createDB();
		this.createTable();
		this.markDate = this.now.getDate();
		// this.addDataTxt();
		// this.addDataSrs();
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
				txt: `++tid, sid, tag, date, last, head, body`,
				srs: `++sid, tag, date, color, head, body`,
				tag: `++gid, head`,
			});
			// tid: Number, sid: Number, tag: Array, date: Date, last: Date, head: String, body: String
			// sid: Number, tag: Array, date: Date, color: #***, head: String, body: String
			// gid: Number, head: String
		},
		addDataTxt() {
			this.db.txt.put({sid: 0, tag: ["日暮らし"], date: "2020/10/10", last: "2020/10/25", head: "徒然なるまゝに", body: "つれづれなるまゝに、日暮らし、硯にむかひて、心にうつりゆくよしなし事を、そこはかとなく書きつくれば、あやしうこそものぐるほしけれ。つれづれなるまゝに、日暮らし、硯にむかひて、心にうつりゆくよしなし事を、そこはかとなく書きつくれば、あやしうこそものぐるほしけれ。"});
			this.db.txt.put({sid: 1, tag: ["徒然", "日暮らし"], date: "2020/10/20", last: "2020/10/21", head: "日暮らし", body: "いでや、この世に生れては、願はしかるべき事こそ多かンめれ。<br>御門(みかど)の御位は、いともかしこし。竹の園生(そのふ)の、末葉(すゑば)まで人間の種ならぬぞ、やんごとなき。一(いち)の人の御有様はさらなり、たゞ人(びと)も、舎人(とねり)など給はるきはは、ゆゝしと見ゆ。その子・孫(むまご)までは、はふれにたれど、なほなまめかし。それより下(しも)つかたは、ほどにつけつゝ、時にあひ、したり顔なるも、みづからはいみじと思ふらめど、いとくちをし。"});
			this.db.txt.put({sid: 2, tag: [], date: "2020/10/30", last: "2020/10/30", head: "硯にむかひて", body: "因幡国(いなばのくに)に、何の入道とかやいふ者の娘、かたちよしと聞きて、人あまた言ひわたりけれども、この娘、たゞ、栗をのみ食ひて、更に、米(よね)の類を食はざりれば、「かゝる異様(ことやう)の者、人に見ゆべきにあらず」とて、親許さざりけり。"});
			this.db.txt.put({sid: 2, tag: ["硯"], date: "2020/10/31", last: "2020/10/31", head: "因幡国", body: "因幡国(いなばのくに)に、何の入道とかやいふ者の娘、かたちよしと聞きて、人あまた言ひわたりけれども、この娘、たゞ、栗をのみ食ひて、更に、米(よね)の類を食はざりれば、「かゝる異様(ことやう)の者、人に見ゆべきにあらず」とて、親許さざりけり。"});
		},
		addDataSrs() {
			this.db.srs.put({tag: ["徒然", "日暮らし"], date: "2020/10/22", color: "#fdcfdb", head: "心にうつりゆく", body: "公世(きんよ)の二位にゐのせうとに、良覚僧正(りやうがくそうじやう)と聞えしは、極(きは)めて腹あしき人なりけり。"});
			this.db.srs.put({tag: ["硯"], date: "2020/10/20", color: "#145c8a", head: "よしなし事を", body: "能(のう)をつかんとする人、「よくせざらんほどは、なまじひに人に知(し)られじ。うちうちよく習ひ得(え)て、さし出(い)でたらんこそ、いと心にくからめ」と常に言ふめれど、かく言ふ人、一芸も習(なら)ひ得(う)ることなし。"});
		},
		addDataTag() {
			this.db.tag.put({head: "日暮らし"});
			this.db.tag.put({head: "徒然"});
			this.db.tag.put({head: "硯"});
		},
		getDataTxt(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.txt.get(key).then((obj) => {
					resolve(obj);
				});
			});
		},
		async getDataTxtPromise(key) {
			let res = await this.getDataTxt(key);
			return res;
		},
		getDataSrs(key) {
			const that = this;
			return new Promise(function(resolve){
				that.db.srs.get(key).then(function(obj){
					resolve(obj);
				});
			});
		},
		async getDataSrsPromise(key) {
			let res = await this.getDataSrs(key);
			console.log(res);
			return res;
		},
		switchLoader() {
			this.loaderFlg = this.loaderFlg ? false : true;
			console.log(this.loaderFlg);
		},
		setToday() {
			this.now = new Date();
			this.now = new Date(this.now.getFullYear(), this.now.getMonth(), this.now.getDate());
			this.nowTime = this.now.getTime();
			this.setCurrent({year: this.now.getFullYear(), month: this.now.getMonth()});
		},
		setCurrent(yymm) {
			this.current = new Date(yymm.year, yymm.month);
			this.currentYear = this.current.getFullYear();
			this.currentMonth = this.current.getMonth();
		},
		setCalendarData(arr) {
			this.currentDates = arr;
		},
		setChangeMonth(obj) {
			this.changeMonthList = obj;
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
		openPosts(obj) {
			this.markDate = obj.date;
			this.$refs.posts.setPostsData(this.markDate);
			this.postsFlg = true;
		},
		closePosts() {
			this.postsFlg = false;
		},
		switchView() {
			this.viewFlg = this.viewFlg ? false : true;
		},
		openViewTxtMark(id) {
			this.$refs.view.setTxtData(Number(id), true);
			this.viewFlg = true;
		},
		openViewSrsMark(id) {
			this.$refs.view.setSrsData(Number(id), true);
			this.viewFlg = true;
		},
		openEditTxtMark(obj) {
			this.$refs.edit.setEditTxtData(obj);
			this.editFlg = true;
		},
		openEditSrsMark(obj) {
			this.$refs.edit.setEditSrsData(obj);
			this.editFlg = true;
		},
		closeEdit(id) {
			this.editFlg = false;
			if(this.postsFlg) this.$refs.posts.setPostsData(this.markDate);
			if(this.viewFlg) this.$refs.view.setTxtData(Number(id), true);
		},
		openAnewField(val) {
			this.currentField = "anew";
			this.anewFieldFlg = true;
			this.currentEdit = val;
			console.log("app", this.currentEdit);
		},
	},
	computed: {
	},
});
</script>

<style>
@import "./css/app.css";
</style>
