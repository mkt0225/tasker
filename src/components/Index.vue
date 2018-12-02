<template>
	<el-container class="container">
		<el-header>
			<span class="header-logo"><img src="../assets/logo_blue.jpg" alt="" class="logo-img"></span>
		</el-header>
		<el-main>
			<div id="app">
				<h1>Taskerは,毎日のタスク管理と日報の作成をカンタンにするツールです。</h1>
				<TodoList @callModal="createModal"/>
				<Output :is-modal="isModal" :output-text="outputText" :output-todos="outputTodos" @callModalClose="closeModal"/>
			</div>
		</el-main>
		<el-footer>
			<div class="footer-wrapper">
				<span class="footer-logo">
					<img src="../assets/logo_for_footer.jpg" alt="footer logo" class="footer-logo-img">
				</span>
				<p>Created with <a href="https://jp.vuejs.org/index.html" class="footer-link">Vue.js</a> and <a href="http://element.eleme.io/#/en-US" class="footer-link">Element-UI</a>.</p>
				<div>
                </div>
			</div>
		</el-footer>
	</el-container>
</template>

<script>
import TodoList from './TodoList.vue'
import Output from './Output.vue'

export default {
	components: {
		TodoList, Output
	},
  data () {
    return {
			isModal: false,
			outputTodos: [],
			outputText: '',
			guiding: false
    }
  },
	methods: {
		createModal(todos) {
			var date = new Date()
			var yobi= new Array("日","月","火","水","木","金","土")
			let today = date.getFullYear() + '年' + (date.getMonth()+1) + '月' + date.getDate() + '日(' + yobi[date.getDay()] + ')'
			var goal = localStorage.getItem("thema") || '登録無し'
			this.outputText += today + '\n【現在取り組み中の課題】\n□' + goal + '\n\n【本日完了したタスク】\n'
			for(var i = 0; i < todos.length; i ++){
				if(todos[i].isAlreadyDeleted){ // 削除フラグが立ってるタスクは日報に出力しない
					continue
				}
				if(!todos[i].isAlreadyFinished){ //完了してないタスクは明日以降のタスクに表示する
					continue
				}
				if(todos[i].displaydate == '無し'){
				  this.outputText += '- ' + todos[i].text + '\n'	
				}else{
				  this.outputText += '- ' + todos[i].text + ' [期限日:' + todos[i].displaydate + ']\n'
				}
			}
			this.outputText += '\n【明日以降のタスク】\n'
			for(var i = 0; i < todos.length; i ++){
				if(todos[i].isAlreadyDeleted){ // 削除フラグが立ってるタスクは日報に出力しない
					continue
				}
				if(todos[i].isAlreadyFinished){ //完了しているタスクは本日完了したタスクに表示する
					continue
				}
				if(todos[i].displaydate == '無し'){
				  this.outputText += '- ' + todos[i].text + '\n'	
				}else{
				  this.outputText += '- ' + todos[i].text + ' [期限日:' + todos[i].displaydate + ']\n'
				}
			}
			this.outputText += '\n【感想】\n'
			this.isModal = !this.isModal  // modalを起こす
		},
		closeModal() {
			this.isModal = !this.isModal // modalを閉じる
			this.outputText = ''
		},
	}
}
</script>

<style scoped>
*, *::before, *::after {
	box-sizing: border-box;
}

#app {
	max-width: 700px;
	margin: 0 auto;
	line-height: 1.4;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #606266;
}
#app h1 {
	width: 70%;
	margin: 20px auto;
}

.container {
	background-color: white;
}

h1 {
	text-align: center;
}

header {
	padding-top: 25px;
}
main {
	margin-top: 30px;
}
footer {
	margin-top: 100px;
	background-color: #909399;
	padding: 0;
	color: white;

}
.footer-wrapper {
	margin:0 auto;
	display: flex;
	justify-content: space-between;
	align-items: center;
	width: 70%;
}

.header-logo {
	line-height: 60px;
	font-weight: bold;
	font-size:36px;
	float: left;
	color: #409EFF;
	margin-left: 8px;
}

.logo-img {
	width: 230px;
	height: auto;
}

.footer-logo {
	margin-top: 20px;
	margin-left: 15px;
	line-height: 60px;
	font-weight: bold;
	font-size:36px;
	float: left;
	color: #409EFF;
	margin-left: 8px;
}

.footer-logo-img {
	width: 150px;
	height: auto;
}
.footer-link:link {
	color: #409EFF;
}

.footer-link:visited {
	color: white;
}
.information {
	line-height:60px;
	float:right;
	color:#409EFF;
}

</style>
