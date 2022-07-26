<template>
  <div class='container'>
    <el-card>
      <!-- 上面的部分 -->
      <el-form label-width="80px" size="small">
        <el-row>
          <el-col :span="6">
            <el-form-item label="关键字">
              <el-input style="200px" v-model="inputValue" placeholder="根据编号搜索"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6" :offset="12">
            <el-form-item style="text-align:right">
              <el-button @click="resetInput">清除</el-button>
              <el-button type="primary" @click="search">搜索</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>

      <!-- alert条 -->
      <el-alert
            :title="`数据一共${total}条`"
            type="info"
            show-icon
            :closable="false"
            style="margin-bottom:15px"
      ></el-alert>

      <!-- table表格 -->
      <el-table :data="tableList">
        <el-table-column label="编号" prop="id" width="210px"></el-table-column>
        <el-table-column label="题型" width="150px">
          <template #default="{row}">
            <!-- <span>{{transType(row.questionType)}}</span> -->
             {{ row.questionType|tanslateType}}
          </template>
        </el-table-column>
        <el-table-column label="题目编号" width="210px">
          <template #default="{row}" >
             <div class="table-questId"
                  v-for="item in row.questionIDs"
                  :key="item.number"
                  @click="questPreview(item.id)"
              >
               <a>{{item.number}}</a>
             </div>
          </template>
        </el-table-column>
        <el-table-column label="录入时间" width="210px">
          <template  #default="{row}">
            {{row.addTime | parseTimeByString}}
          </template>
        </el-table-column>
        <el-table-column label="答题时间(s)" prop="totalSeconds"></el-table-column>
        <el-table-column label="正确率(%)" prop="accuracyRate"></el-table-column>
        <el-table-column label="录入人" prop="userName"></el-table-column>
        <el-table-column label="操作">
          <template #default="{row}">
            <el-button type="danger" icon="el-icon-delete" plain circle @click="delQuestion(row.id)"></el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页器 -->
      <div style="float:right; margin:15px 0">
        <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="listData.page"
          :page-sizes="[5, 10, 15, 20]"
          :page-size="listData.pagesize"
          layout="prev,pager,next,sizes,jumper"
          :total="total"
        >
        </el-pagination>
      </div>

      <!-- 预览部分 -->
      <QuestionsPreview
          :showDialog.sync="showDialog"
          :perviewId="perviewId"
          >
      </QuestionsPreview>
    </el-card>
  </div>
</template>

<script>
import { randoms, removeRandoms } from '../../api/interview/questions'
// import obj from '../../constant/queation-randoms'
import QuestionsPreview from '../components/questions-previewstwo.vue'
// import { tanslateType } from '../../filters/index'
// const { questType } = obj

export default {
  components: {
    QuestionsPreview
  },
  data () {
    return {
      tableList: [],
      total: 0,
      listData: {
        page: 1,
        pagesize: 20,
        keyword: null
      },
      inputValue: '',
      showDialog: false,
      perviewId: 0
    }
  },
  created () {
    this.getTableList()
  },
  methods: {
    async getTableList () { // todo 获取组题列表
      const { data: { counts, items } } = await randoms(this.listData)
      // console.log(items)
      this.tableList = items
      this.total = counts
    },
    handleSizeChange (val) { // todo 更改每页条数
      console.log(`每页 ${val} 条`)
      this.listData.pagesize = val
      this.getTableList()
      this.listData.page = 1
    },
    handleCurrentChange (val) { // todo 更改页数
      console.log(`当前页: ${val}`)
      this.listData.page = val
      this.getTableList()
    },
    // transType (type) { // todo 映射试题类型
    //   // console.log(type)
    //   return questType.find(item => item.id === +type).value
    // },
    resetInput () { // todo 点击清空按钮
      this.inputValue = ''
    },
    delQuestion (id) { // todo 删除
      // console.log(id)
      this.$confirm('确认要删除吗？不可逆', '温馨提示', {
        type: 'warning'
      }).then(async () => {
        await removeRandoms({ id })
        // console.log(res, 110)
        if (this.tableList.length === 1 && this.page > 1) {
          this.page--
        }
        this.$message.success('删除成功')
        this.getTableList()
      }).catch(() => {})
    },
    search () { // todo 根据id搜索 页面仅显示这一项
      if (!this.inputValue) {
        return this.getTableList()
      }
      // const result = this.tableList.find(item => item.id === this.inputValue)
      // this.tableList = [...result]
      this.listData.keyword = this.inputValue
      this.getTableList()
    },
    async questPreview (id) {
      this.showDialog = true
      this.perviewId = id
    }

  }

}
</script>

<style scoped lang='scss'>
::v-deep{

  .el-table .cell .table-questId {
    color: #409EFF;
  }
  .el-pagination.is-background .el-pager li {
    margin:0 5px;
    background-color: #f4f4f5;
    border-radius: 2px;
    color: #606266;
    width: 30px;
  }

  .el-pager li.active{
    color: #fff;
    width: 10px;
    height: 30px;
    background-color: #409EFF;
  }
}

</style>
