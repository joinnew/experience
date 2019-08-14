<script>
  export default {
    data () {
      return {
        records: [],
        editData: {
          index: null,
          money: null
        }
      }
    },

    methods: {
      onEdit (row, index) {
        // 每次支持一行数据操作
        if (this.editData.index !== null) {
          // 提示是否放弃之前未保存的操作
        }

        // 判断哪一行正在编辑，可以是每一行数据的唯一值，也可以是通过index值来区分
        Object.assign(this.editData, row, { index }) // 大概的赋值，需要将当前编辑的输入框的值赋值
      },

      checkInput (val, type) {

      }
    }
  }
</script>

<template>
  <div class="edit-table">
    <el-table :data="records">
      <el-table-column label="金额">
        <template slot-scope="{row, $index}">
          <!-- 用于区分当前正在编辑的是哪一行数据 -->
          <div
            v-if="editData.index === $index"
            class="input-change"
          >
            <el-input
              v-model="editData.submitAmount"
              @change="(val) => checkInput(val, 'amount')"
            ></el-input>
            <!-- checkInput携带多个参数传递 -->

            <div
              class="error"
              v-if="amountError"
            >{{ amountError }}</div>
          </div>

          <span v-else>{{ row.submitAmount }}</span>
        </template>
      </el-table-column>

      <el-table-column label="操作">
        <template slot-scope="{row, $index}">
          <el-button @click="onEdit(row, $index)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>