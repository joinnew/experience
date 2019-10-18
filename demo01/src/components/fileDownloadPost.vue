<script>
// 业务中有时候存在：文件post下载格式
const SUCCESS_CODE = 0

export default {
  methods: {
    async handlePrint (id = 0) {
      let tip = type => `打印文件${type}`
      let hash = this.getPrintHash(id)
      axios({
        method: 'post',
        url: 'xxx', // 提供的文件下载地址,这边就不提供地址了
        data: hash,
        responseType: 'blob'
      })
        .then((res) => {
          const content = res
          const blob = new Blob([content.data], { type: 'application/zip' }) // 指定文件的格式是zip,后端传递的格式是zip的

          // 不论错误与否，此时返回的结果都是blob格式
          let reader = new FileReader()
          reader.onload = e => {
            let temp = e.target.result
            if (/^{(.*)}$/.test(temp)) {
              let ret = JSON.parse(e.target.result)
              if (ret.hasOwnProperty('code') && ret.code !== SUCCESS_CODE) { // 项目接口通过code值来判断是否请求成功（跟status不一样）,也可以改成其他的
                this.shortcuts.errNotify(ret, tip('失败'))
                return
              }
            }

            let fileName = moment(new Date()).format('YYYY-MM-DD') // 设置下载的文件名
            if ('download' in document.createElement('a')) { // 非IE下载
              const elink = document.createElement('a')
              elink.download = fileName
              elink.style.display = 'none'
              elink.href = URL.createObjectURL(blob)
              // 在Chrome、Firefox、Safari、360、EdgeHtml浏览器中，均可以成功下载文件，但是在Edge中，会报错。
              // 原因：在Edge中使用Blob生成的是不带域名的blob链接，如下：
              // 类似这样的地址：blob:00F0B45-DD4E-4F4F-9B15-000368F15E20，也就是说无法通过a链接下载
              // 而在chrome等浏览器下，生成的是带域名的， 如： http://xxx.xxx.biz/86e01467-6...;
              // 所以针对不同浏览器采用不同的api进行处理
              document.body.appendChild(elink)
              elink.click()
              URL.revokeObjectURL(elink.href) // 释放URL 对象
              document.body.removeChild(elink)
            } else { // IE10+下载
              window.navigator.msSaveBlob(blob, fileName)
            }
          }
          reader.readAsText(blob)
        })
        .catch(err => {
          this.shortcuts.errNotify(err, tip('异常'))
        })
    }
  }
}
</script>

<template>
  <div class="file-download-post">
    <el-button
      type="text"
      @click="handlePrint(id = 1)"
    >
      打印
    </el-button>
  </div>
</template>
