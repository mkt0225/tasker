<template>
	<div class="wrapper">
		<BaseInputText
			v-model="newTodoText"
			@keypress.enter="addTodo"
			@recieveNewTask="addTodo"
		/>
		<div v-if="todos.length">
			<div class="heading-label">
				<span>タスク</span>
				<span>期限日</span>
				<span>状態</span>
				<span>削除</span>
			</div>
			<!-- タスク数が0⇄1を跨ぐタイミングからソートできなくなる -->
			<ul id="sortable">
					<TodoListItem
						v-for="todo in sortedtodos"
						:key="todo.id"
						:todo="todo"
						@remove="removeTodo"
						@changeFlag="changeFinishFlag"
						@redoFlag="changeRedoFlag"
						@callEditModalClose="editModalClose"
						v-if="!todo.isAlreadyDeleted"
						:class="sort-elements"
					/>
			</ul>
			<div class="all-delete-button-container">
				<el-button type="danger" @click="deleteAllTask" class="all-delete-button">タスクをすべて削除する</el-button>
			</div>
		</div>
		<p class="empty-label" v-else>
			現在登録中のタスクはありません。
		</p>
		<div class="lower-button">
			<el-button type="primary" @click="sendOutput" class="modal-button">日報を書く</el-button>
		</div>
	</div>
</template>

<script>
import BaseInputText from './BaseInputText.vue'
import TodoListItem from './TodoListItem.vue'


if(localStorage.getItem("idLength")){
	var nextTodoId = parseInt(localStorage.getItem("idLength"))
}else{
	var nextTodoId = 0
}

export default {
	components: {
		BaseInputText, TodoListItem
	},
  data () {
    return {
			newTodoText: '',
			newDate: 'test',
      todos: [
			]
    }
  },
	computed: {
		sortedtodos: function() {
    	function compare(a, b) {
				if(a.rowdate === b.rowdate){
					return 0;
				}else if(a.rowdate === null){
					return 1;
				}else if(b.rowdate === null){
					return -1;
				}else if (a.rowdate < b.rowdate){
        	return -1;
				}else if (a.rowdate > b.rowdate) {
        	return 1;
				}
      	return 0;
    	}
    	return this.todos.sort(compare);
  	}
	},
	methods: {
		addTodo () {
			const trimmedText = this.newTodoText.trim()
			const date = this.$children[0].$children[2].$data.value // DatePickerから日付を取る変数だが、$childrenで指定しているため全然疎結合じゃない。直したい。
			var trimmedDate = ''
			if(date === null || date === undefined) {
				trimmedDate = "無し"
			}else {
				trimmedDate = date.getFullYear() + "/" + parseInt(date.getMonth() + 1) + "/" + date.getDate() + "まで"
			}

			if (trimmedText) {
				nextTodoId = parseInt(nextTodoId + 1)

				var tmpobj = {
					id: nextTodoId,
					text: trimmedText,
					comment: '',
					displaydate: trimmedDate,
					rowdate: date,
					isAlreadyFinished: false,
					isAlreadyDeleted: false,
					isEdit: false
				}
				this.todos.push(tmpobj)
				var keyName = 'todo' + nextTodoId
				localStorage.setItem(keyName,JSON.stringify(tmpobj))
				localStorage.setItem("idLength",nextTodoId)
				this.newTodoText = ''
				this.$notify.success({
	          title: 'Add Task!',
	          message: '新しいタスクを追加しました'
	        });
			}
			if(trimmedText === "" ){
				this.$message({
	          message: '入力内容がありません',
						type: 'warning'
	        });
			}
		},
		removeTodo (idToRemove) {
      		this.$confirm('このタスクを削除しますか？', '確認', {
        	confirmButtonText: ' はい ',
        	cancelButtonText: 'キャンセル',
        	type: 'warning',
		    center: true
      		}).then(() => {
				// 削除したいid自体をemitしてきてるのに配列のindex番号でやってた
				// 直接todosをforで回してtodo.idと合致してれば消す方式でやらないと消すごとに違うtodoを消す可能性が出て来る
				this.todos.forEach(function(todo){
					if(todo.id == idToRemove){
						todo.isAlreadyDeleted = true
						var remName = 'todo' + todo.id
						localStorage.removeItem(remName)
					}
				})
        	this.$notify({
				title: 'Delete Complete!',
          		type: 'success',
          		message: '削除完了しました'
        	});
      }).catch(() => {
			   	this.$message({
          type: 'info',
          message: '削除をキャンセルしました'
        });
      });
		},
		changeFinishFlag(idToFinish) {
			this.todos.forEach(function(todo){
				if(todo.id == idToFinish){
					todo.isAlreadyFinished = !todo.isAlreadyFinished
					var storagedName = 'todo' + todo.id
					localStorage.setItem(storagedName,JSON.stringify(todo))
				}
			})
			this.$notify({
            title: 'Complete Task!',
            message: 'タスクが完了しました',
            type: 'success'
      });
		},
		changeRedoFlag(idToRedo) {
			this.todos.forEach(function(todo){
				if(todo.id == idToRedo){
					todo.isAlreadyFinished = !todo.isAlreadyFinished
					var storagedName = 'todo' + todo.id
					localStorage.setItem(storagedName,JSON.stringify(todo))
				}
			})
			this.$notify({
          title: 'Redo Complete',
          message: '未完了状態に戻しました',
          type: 'warning'
      });
		},
		sendOutput() {
			this.$emit('callModal',this.todos)
		},
		deleteAllTask() {
			this.$confirm('すべてのタスクを削除しますか？', '確認', {
        confirmButtonText: ' はい ',
        cancelButtonText: 'キャンセル',
        type: 'error',
		    center: true
      }).then(() => {
				for(var j in localStorage) {
					if(j.includes('todo')){ // タスク以外にもlocalStorageを使っているためlocalStorageだけに絞り込む
						localStorage.removeItem(j)
					}
		  	}
				this.todos = []
				this.$message({
	          message: 'すべてのタスクを削除しました',
	          type: 'success'
	        });
      }).catch(() => {
			   	this.$message({
          type: 'info',
          message: '削除をキャンセルしました'
        });
      });
		},
		editModalClose(idToCloseEdit) {
			this.todos.forEach(function(todo){
				if(todo.id == idToCloseEdit){
					todo.isEdit = false
				}
			})
		}
	},
	created: function() {
		for(var j in localStorage) {
			if(j.includes('todo')){ // タスク以外にもlocalStorageを使っているためlocalStorageだけに絞り込む
				var storagedItem = localStorage.getItem(j)
				storagedItem = JSON.parse(storagedItem)
				this.todos.push(storagedItem)
			}
  		}

		jQuery( function() {
	        jQuery( '#sortable' ) . sortable( {
	                cursor: 'move',                     //移動中のカーソル
	                opacity: 0.7,                       //移動中の項目の透明度
	                placeholder: "ui-state-highlight",  //ドロップ先の色指定(Styleで指定可能)
	                forcePlaceholderSize: true ,         //trueでドラッグした要素のサイズを自動取得できる
									axis: 'y',
									change: function(event,ui){
										console.log(event, ui)
									}
	        } );
	        $( '#sortable' ).disableSelection();

	    } );

	}
}
</script>

<style >
.heading-label {
	margin-top: 40px;
	display: flex;
	justify-content: space-between;
}
.heading-label span:nth-child(1){
	margin-left: 30%;
}
.heading-label span:nth-child(2){
	margin-right: -16%;
}
.heading-label span:nth-child(4){
	margin-left: -21%;
	margin-right: 6%;
}
.heading-label span {
	font-weight: bold;
	font-size: 1.1rem;
}
.lower-button {
	margin: 40px auto;
}
.all-delete-button-container {
	height: 50px;
	margin-top: 5%;
	margin-right: 4%;
}
.all-delete-button {
	font-size:0.8rem;
	padding:10px 15px;
	float: right;
}
.modal-button {
	width: 200px;
}

.wrapper {
	margin-top: 50px;
}
</style>
