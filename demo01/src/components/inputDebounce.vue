<script>
  export default {
    data () {
      return {
        inputValue: '',
        timer: null
      }
    },

    watch: {
      inputValue (val) {
        if (!val) {} // 当内容清空时候的操作
        if (this.timer) {
          clearTimeout(this.timer)
        }

        this.timer = setTimeout(() => {
          this.setSearchData(val)
        }, 500) // 在用户输入内容结束后500ms之后，就会发送请求
      }
    },

    methods: {
      setSearchData (val) { // 请求数据的函数

      }
    }
  }
</script>

<template>
  <div class="input-debounce">
    <el-input
      v-model="inputValue"
      palceholder="输入内容进行搜索"
    ></el-input>
  </div>
</template>