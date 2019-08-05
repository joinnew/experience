<script>
  export default {
    data () {
      return {
        filterInput: '',
        selectVal: '',
        isExpanded: false, // 是否展开
        allList: [] // 随意取名，需要最先把所有的数据请求到。我这边就不模拟请求了。
      }
    },

    methods: {
      // 通过单选区域的高度，来判断是否需要出现按钮
      calculateHeight () {
        this.$nextTick().then(() => {
          let contentHeight = this.$refs.radioSelectBox.clientHeight
          if (contentHeight > 100) {
          // 100px是设置的可选选项的max-height值
            this.isLongRadio = true
          } else {
            this.isLongRadio = false
          }
        })
      }
    },

    computed: {
      filterList () {
        // 不区分大小写匹配
        let input = this.filterInput.toLowerCase()
        this.calculateHeight()
        if (input && input.length) {
          return this.allList.filter(item => (item.name.toLowerCase().includes(input)))
        }

        //  如果没有内容输入或者清空的，就返回所有的数据
        return this.allList 
      }
    }
  }
</script>

<template>
  <div class="local-filter">
    <el-form>
      <el-form-item label="选择xxx：">
        <!-- input输入的值，是用来过滤本地拿到的所有数据的 -->
        <el-input
          style="width: 320px;"
          v-model="filterInput"
          placeholder="请输入xxx进行搜索"
        ></el-input>

        <!-- 展示过滤出来的结果，最多展示两行(以单选框的形式出现)，内容超过两行则显示’展开‘且隐藏其他内容，不超过则完整展示，'展开'不出现 -->
        <div
          :class="['margin-top-20', isExpanded ? 'expanded' : 'common']"
        >
          <div ref="radioSelectBox">
            <el-radio-group v-model="selectVal">
              <el-radio
                v-for="item in filterList"
                :key="item.id"
                :label="item.id"
                border
              >{{ item.name }}</el-radio>
            </el-radio-group>
          </div>
        </div>

        <el-button
          v-show="isLongRadio"
          @click="isExpanded = !isExpanded"
          type="text"
        >
          {{ isExpanded ? '收起' : '展开' }}
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<style scoped>
  .el-form-item__content {
    max-width: 900px;
  }

  .el-radio {
    margin-bottom: 10px;
    margin-left: 0!important;
    margin-right: 10px;
  }

  /* 设置height为auto */
  .expanded {
    height: auto; 
    transition: .2s;
  }

  .common {
    max-height: 100px;
    overflow: hidden;
    transition: .2s;
  }
</style>