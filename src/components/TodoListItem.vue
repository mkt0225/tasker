<template>
  <div class="wrapper-item">
    <!--
    <div class="task-modal" v-if="todo.isEdit == true">
      <div>
        <!-- todo: ここにタスク編集機能をつける
        <el-button type="info" @click="$emit('callEditModalClose',todo.id)">キャンセル</el-button>
        <el-button type="primary" @click="">送信する</el-button>
      </div>
    </div>
    -->
    <li @click="showItself">
      <div>
        <div class="task__text">
          <span v-bind:class="{ finished: todo.isAlreadyFinished}" class="collapser">{{ todo.text }}</span>
        </div>
        <div class="task__icons">
          <span>{{ todo.displaydate }}</span>
          <transition name="fade" mode="out-in">
            <el-button type="success" icon="el-icon-check"  v-if="!todo.isAlreadyFinished" @click="$emit('changeFlag',todo.id)" class="todo__button"></el-button>
            <el-button type="warning"  v-if="todo.isAlreadyFinished" @click="$emit('redoFlag',todo.id)" class="todo__button"><i class="fas fa-undo"></i></el-button>
          </transition>
          <el-button type="danger" icon="el-icon-delete" @click="$emit('remove', todo.id)" class="todo__button"></el-button>
        </div>
      </div>
    </li>
  </div>
</template>

<script>
export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: {
    showItself() {
      console.log(this.todo)
      this.todo.isEdit = true
    }
  }
}
</script>

<style scoped>
.wrapper-item {
  min-height:80px;
  cursor: pointer;
}
.wrapper-item:hover {
  background-color: #E4E7ED;
}
  li {
    max-width: 600px;
    margin: 0 auto;
    padding-top: 2%;
  }
  li div {
    display: flex;
    align-items: center;
  }
  .task__text {
    font-size: 1.2rem;
    width: 60%;
    display: flex;
  }
  .task__text span {
    padding:5px;
    align-items: center;
    word-break: break-all;
    text-align: left;
  }
  .task__icons {
    width: 300px;
    margin-left: 10px;
  }
  .task__icons span{
    width: 150px;
    margin-right: 10px;
  }
  .finished {
    text-decoration: line-through;
  }
  .isLimitToday {
    color: red;
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity .4s ease;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }
  .task-modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(51,51,51,0.5);
    z-index: 2;
  }
</style>
