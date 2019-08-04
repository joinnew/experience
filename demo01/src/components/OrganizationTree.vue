<script>
  // 使用树来展示某些层级关系，但是又想只展示部分层级关系内容的业务需求时。
  const FILTER_GROUP_NAME = ['大佬-萌新，菜鸟'] // 当前是排除根节点(根节点的层级不用写在里面)之外的层级关系，大佬下面的萌新和菜鸟内容需要展示,其他层级的再另外写入即可
  export default {
    data() {
      return {
        data: [],
        defaultProps: {
          children: 'childs', // 需要配置成对应的子节点的变量名
          label: 'name'
        },
        FILTER_GROUP_NAME
      }
    },

    methods: {
      onInsertPatch (list) {
        this.FILTER_GROUP_NAME.forEach(keyword => {
          list.map(item => {
            let keywordList = keyword.split('-')
            if (keywordList[0].includes(item.name)) {
              item.patchWord = keyword
              item.patchLevel = keywordList.length // 主要是匹配的层级
              item.patchTime = keywordList[(keywordList.length) - 1].split(',').length // 最后一阶段的匹配次数
            } else {
              return false
            }
          })
        })

        // 过滤掉没有匹配词的部门
        return list.filter(item => item.patchWord)
      },

      filterGroup (val) {
        const copyTree = cloneDeep(val)
        copyTree[0].childs = this.onInsertPatch(val[0].childs)

        let len = copyTree[0].childs.length - 1
        let totalIndex = 1 // 层级,从第一层开始，patchLevel
        let patchIndex = 0 // 第一级的下标，因为匹配字段和匹配次数都是在第一级设置的
        let curPatchTime = 0

        let _doRecursive = (item, totalIndex) => {
          if (len >= patchIndex && copyTree[0].childs[patchIndex].patchTime > curPatchTime) {
            if (copyTree[0].childs[patchIndex].patchLevel < totalIndex) return false
            item.disabled = true // 前面几级都要禁止点击
            if (item.childs && item.childs.length) {
              for (let i = 0; i < item.childs.length; i++) {
                let child = item.childs[i]
                let firstChild = copyTree[0].childs[patchIndex] // 获取第一级的数据
                if (firstChild && firstChild.patchWord.includes(child.name)) {
                  let lastFetchWord = firstChild.patchWord.split('-')[firstChild.patchLevel - 1]
                  if (lastFetchWord.includes(child.name)) curPatchTime++
                  _doRecursive(child, ++totalIndex)
                  --totalIndex
                } else {
                  item.childs.splice(i, 1)
                  i--
                }
              }
            }
          } else {
            patchIndex++
            curPatchTime = 0
            return false
          }
        }

        _doRecursive(copyTree[0], totalIndex)

        copyTree.filter(item => item.childs.length)

        copyTree[0].disabled = true
        return copyTree
      },

      loadRecords () {
        // 请求到数据之后，形成相应的层级格式再调用filterGroup进行转换
      },

      initTree (data) { // 针对后端传过来的不是[{ children: []}]刚好的格式处理。传输的结构是节点都是一个个的对象，通过parentId来形成树结构
        const map = {}
        let val = []

        data.forEach(item => {
          delete item.children
        })
        data.forEach(item => {
          map[item.id] = item
        })
        data.forEach(item => {
          const parent = map[item.parentId]
          if (parent) {
            (parent.children || (parent.children = [])).push(item)
          } else {
            val.push(item)
          }
        })
        return val
      }
    }
  }
</script>

<template>
  <div class="organization-tree">
    <el-tree
      :data="data"
      :props="defaultProps"
      node-key="id"
    ></el-tree>
  </div>
</template>