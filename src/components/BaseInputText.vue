<template>
  <div class="menu">
    <h2 class="thema-heading">現在取り組み中の課題：</h2>
    <p v-if="thema" class="thema">{{ thema }}</p>
    <p v-else>現在登録中の課題はありません。</p>
    <el-button @click="dialogThemaVisible = true">追加・変更</el-button>
    <el-dialog title="課題の追加・変更" :visible.sync="dialogThemaVisible">
      <el-input v-model="thema" @change="changeThema"></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogThemaVisible = false">キャンセル</el-button>
        <el-button type="primary" @click="changeThema">変更</el-button>
      </span>
    </el-dialog>
    <h2>タスクを入力してください</h2>
    <input
      type="text"
      class="input"
      :value="value"
      v-on="listeners"
      placeholder="新しいタスク"
    >
    <div class="inner-menu">
      <DatePicker></DatePicker>
      <div  id="button-area" 
            v-for="tag in tagArray"
            :key="tag.id"
      >
        <el-button type="primary" round>{{ tag.name }}</el-button>
      </div>
      <el-input v-model="tagString" @change="changeTag"></el-input>
      <el-button type="primary" @keyup.enter="sendingNewTask">追加する</el-button>
    </div>
  </div>
</template>

<script>
import DatePicker from './DatePicker.vue'



export default {
  data() {
    return {
      isClose: true,
      thema: localStorage.getItem("thema"),
      dialogThemaVisible: false,
      tagString: "",
      tagArray: [],
      nextTagId: 1
    }
  },
  components: {
    DatePicker
  },
  props: {
    value: {
      type: String,
      default: '',
    },
    newDate: {}
  },
  computed: {
    listeners() {
      return {
        // Pass all component listeners directly to input
        ...this.$listeners,
        // Override input listener to work with v-model
        input: event => this.$emit('input', event.target.value)
      }
    }
  },
  methods: {
    sendingNewTask() {
      this.$emit('recieveNewTask')
    },
    changeThema() {
      localStorage.setItem("thema",this.thema)
      this.dialogThemaVisible = false
    },
    changeTag() {
      var tag = {id: this.nextTagId,name: this.tagString}
      this.tagArray.push(tag);
      this.nextTagId += 1;
      this.tagString = "";
    }
  },
}
</script>

<style scoped>
.menu {
  max-width: 700px;
  text-align: center;
  border: 1px solid #DCDFE6;
}

.input {
  font-size: 1.1rem;
  width: 90%;
  padding: 8px 10px;
  margin-left: 10px;
  margin-bottom: 10px;
  border: none;
  border: 1px solid #DCDFE6;
  display: inline-block !important;
}

.inner-menu {
  margin-top:8px;
  margin-bottom: 30px;
  height:40px;
}

.inner-menu div {
  float:left;
  margin-left: 29px;
}
.inner-menu button {
  width: 130px;
  height: 45px;
 float: right;
 margin-right:20px;
}
.thema {
  font-size: 1.4rem;
}

.thema__input {
  width:50%;
  font-size: 1.1rem;
}
</style>
