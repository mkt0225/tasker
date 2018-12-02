<template>
  <div>
    <div v-if="isModal" class="modal-window">
      <h2>日報編集</h2>
      <form action="http://ec2-18-219-211-230.us-east-2.compute.amazonaws.com/json.php" method="post" name="sendform">
        <div class="input-name">
          <p v-if="noNameError" class="name-error">*投稿者名は必須です</p>
          <span>投稿者名：</span>
          <el-input placeholder="名前を入力してください" v-model="inputName" class="input__name" name="name">{{ inputName }}</el-input>
          <div></div>
          <el-checkbox v-model="checked">次回以降の名前の入力を省略する</el-checkbox>
        </div>
        <div class="channel-select-container">
          <p v-if="noChannelError" class="name-error">*チャンネルの指定は必須です</p>
          <span class="post-channel__label">投稿先チャンネル：</span>
          <el-select v-model="value" placeholder="Channel" name="channel" class="channel-select">
            <el-option
             v-for="item in options"
             :key="item.value"
             :label="item.label"
             :value="item.value">
            </el-option>
          </el-select>
        </div>
       <textarea name="text" rows="8" cols="80" class="input-area">{{ outputText }}</textarea>
       <div>
         <el-button type="info" @click="$emit('callModalClose')">キャンセル</el-button>
         <el-button type="primary" @click="sendSlack">送信する</el-button>
       </div>
     </form>
    </div>
  </div>
</template>

<script>
let storagedName = localStorage.getItem('name') || ''
  export default {
    data(){
      return {
        checked: false,
        dialogVisible: false,
        noNameError: false,
        noChannelError: false,
        inputName: storagedName,
        options: [{
          value: 'bot_nkgw',
          label: 'テスト(#bot_nkgw)'
        },{
          value: 'C9ZFXGTBJ',
          label: '#rookie_2018_report'
        }],
        value: ''
      }
    },
    methods: {
      sendSlack() {
        if(!this.inputName){
          this.noNameError = true
          return
        }
        if(!this.value){
          this.noChannelError = true
          return
        }
        if(this.checked){
          localStorage.setItem("name",this.inputName)
        }
        document.sendform.submit();
      },
    },
    props: ['isModal','outputText','outputTodos'],
  }

</script>

<style>
.modal-window {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(51,51,51,0.5);
  z-index: 2;
  }
.modal-window h2 {
  color:white;
  margin-bottom: 20px;
}
.input-area {
  height:60vh;
  font-size:16px;
  margin-bottom: 10px;
}
.input__name {
  width:30% !important;
}

.input-name {
  margin-bottom: 10px;
}
.input-name div {
  margin-bottom:5px;
}
.input-name span {
  color:white;
  font-weight: bold;
}
.form-box {
  margin-bottom: 10px;
}
.form-box span {
  color:white;
  font-weight: bold;
  font-size:16px;
}
.form-box {
  display: flex;
  align-items: center;
  justify-content:center;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(51,51,51,0.7);
  z-index: 3;
}

.inner-form-box {
  width: 500px;
}

.inner-form-box h2 {
  margin: 0;
}

.inner-form-box div {
  margin-top: 30px;
}

.inner-form-box div div {
  margin-top:0;
}

.name-error {
  color: #ff2041;
  margin: 0;
}
.send-button {
  border-radius: 6%;
  padding:10px 15px;
  background-color: #409EFF;
  color:white;
  border:none;
  font-size: 0.9rem;
}
.send-button:hover {
  background-color: #81cfff;
}
.post-channel__label {
  color:white;
  font-weight: bold;
}
.channel-select {
  width:30%;
}
.channel-select-container {
  margin-bottom: 20px;
  margin-left: -4%;
}
</style>
