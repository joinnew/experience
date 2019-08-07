<script>
  export default {
    data () {
      return {
        records: []
      }
    }
  }
</script>

<template>
  <div class="total-sum-table">
    <!-- show-summary就是使用表格的合计功能，sum-text自定义合计那一行数据的名字。想要这个功能实现，必须要在el-table-column对应的数据上添加prop(不管是否有slot-scope)。
    不需要合计的那一列就不用添加prop,那么那一列则默认为禁用状态 -->
    <el-table
      :data="records"
      show-summary
      sum-text="总计"
    >
      <el-table-column label="名字">
        <template slot-scope="scope">
          {{ scope.row.name }}
        </template>
      </el-table-column>

      <el-table-column label="条数" align="center" prop="count"></el-table-column>
      <el-table-column label="金额" align="center" prop="amount"></el-table-column>

      <!-- 默认合计的数据会在最后,所以slot="append"的内容会在合计之前出现 -->
      <div slot="append">
        <!-- 点击新增数据，如果想在表格内出现一行新数据，可以直接对this.records.push操作 -->
        <el-button size="small" type="primary">新增数据</el-button>
      </div>
    </el-table>
  </div>
</template>