<template>
    <el-date-picker
      v-model="value"
      type="date"
      placeholder="期限日を選ぶ"
      :picker-options="pickerOptions1"
      >
    </el-date-picker>
</template>

<script>
  export default {
    data() {
      return {
        pickerOptions1: {
          disabledDate(time) {
            return time.getTime() < Date.now() - 3600 * 1000 * 24;
          },
          shortcuts: [{
            text: '今日',
            onClick(picker) {
              picker.$emit('pick', new Date());
            }
          }, {
            text: '明日',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() + 3600 * 1000 * 24);
              picker.$emit('pick', date);
            }
          }, {
            text: '一週間後',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() + 3600 * 1000 * 24 * 7);
              picker.$emit('pick', date);
            }
          }]
        },
        value: null
      };
    },
  };
</script>
