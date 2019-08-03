<script>
  export default {
    methods: {
      // 复制内容到剪切板上
      handleCopy(content = '') { // 实际中有关数据内部的部分一般是通过变量渲染出来的，到时候把要复制的内容传递过来就可以了。
        let text = content
        let oInput = document.createElement('input')
        oInput.value = text
        document.body.appendChild(oInput)
        oInput.select() // 选择对象;
        document.execCommand("Copy") // 执行浏览器复制命令
        this.shortcuts.msg('已成功复制到剪切板')
        oInput.remove()
      }
    }
  }
</script>

<template>
  <div class="click-copy-text">
    <p>内容部分
      <el-tooltip content="复制">
        <i class="el-icon-document-copy" @click="handleCopy"></i> 
      </el-tooltip>
    </p>
  </div>
</template>