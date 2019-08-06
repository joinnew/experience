<script>
  // 对应需求：有时候父子内容通过el-table展示，但是父子展示的内容和字段不一致的情况，那就只能通过expand来展示子数据。
  // 内容不一致，但是又需要对应位置对齐，而且没有子数据的数据，expand展开图标需要消失 
  export default {
    data () {
      return {
        records: [{
          name: '水果',
          num: 200,
          color: 'red, yellow, green',
          level: 2,
          children: [
            {
              cName: '苹果',
              cNum: 100,
              level: 1
            },
            {
              cName: '香蕉',
              cNum: 10,
              level: 2
            }
          ]
        },
        {
          name: '辣条',
          num: 100
        }] // 实际应该是请求接口拿到，仅是为了模拟，就简单行事
      }
    },

    methods: {
      getRowClass ({ row, index }) {
        if (!(row.children && row.children.length)) { // 用来区分某行是否需要展示“展开图标”
          return 'row-expand-cover'
        }
      }
    }
  }
</script>

<template>
  <div class="table-expand">
    <el-table :data="records" :row-class-name="getRowClass">
      <template slot="empty">No data</template>
      <!-- 展示子数据 -->
      <el-table-column type="expand">
        <template slot-scope="scope">
          <el-table
            v-if="scope.row.children && scope.row.children.length"
            :data="scope.row.children"
            class="expand-table"
            :show-header="false"
          >
            <!-- 父元素那里有一个展开图标，那么就会有宽度占用显示。子数据这里需要设置一个空的，宽度可以自行调整。其他需要对齐的地方都可以这样设置 -->
            <el-table-column width="80"></el-table-column> 
            <el-table-column label="名字" prop="cName"></el-table-column>
            <el-table-column label="数量" prop="cNum"></el-table-column>
            <el-table-column width="80"></el-table-column>
            <el-table-column label="等级" prop="level"></el-table-column>
          </el-table>
        </template>
      </el-table-column>

      <el-table-column label="名字" prop="name"></el-table-column>
      <el-table-column label="数量" prop="num"></el-table-column>
      <el-table-column label="颜色" prop="color"></el-table-column>
      <el-table-column label="等级" prop="level"></el-table-column>
    </el-table>
  </div>
</template>

<style>
  /* 设置样式来控制是否展示图标 */
  .row-expand-cover .el-table__expand-column .el-icon{
    visibility:hidden;
  }
</style>
