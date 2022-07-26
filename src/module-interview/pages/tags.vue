<template>
  <div class='container'>
    <!-- 下面是 tags 的页面 -->
    <el-card>
      <!-- 面包屑导航 -->
      <div slot="header" v-if="subject.id && subject.name">
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item>学科管理</el-breadcrumb-item>
          <el-breadcrumb-item>{{subject.name}}</el-breadcrumb-item>
          <el-breadcrumb-item>目录管理</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <!-- 下面是表单入口 -->
      <el-row>
        <el-col :span="18">
          <el-form label-width="80px"  :inline="true" :model="formData" size="small" class="demo-form-inline">
            <el-form-item label="标签名称">
              <el-input  style="200px"  v-model="formData.tagName"></el-input>
            </el-form-item>
            <el-form-item label="状态">
              <el-select v-model="formData.state" placeholder="请选择">
                <el-option label="启用" value="1"></el-option>
                <el-option label="禁用" value="0"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button @click="clearTabs">清除</el-button>
              <el-button type="primary" @click="serchTabs">搜索</el-button>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :span="6" style="text-align:right">
          <el-button v-if="subject.id && subject.name" type="text" icon="el-icon-back" @click="$router.back()">返回学科</el-button>
          <el-button size="small" @click="showDialog()" type="success" icon="el-icon-edit">新增标签</el-button>
        </el-col>
      </el-row>
      <!-- 提示文本入口 -->
      <el-alert
        type="info"
        style="margin-bottom:15px"
        class="alert"
        :closable="false"
        show-icon
        :title="`数据一共 ${ counts } 条`"
      />
      <!-- 表格内容 -->
      <el-table :data="tableData">
        <el-table-column type="index" :index="indexFn" label="序号" width="80px"></el-table-column>
        <el-table-column prop="subjectName" label="所属学科"></el-table-column>
        <el-table-column prop="tagName" label="标签名称"></el-table-column>
        <el-table-column prop="username" label="创建者"></el-table-column>
        <el-table-column label="创建日期">
          <template #default="{ row }">{{ row.addDate | parseTimeByString }}</template>
        </el-table-column>
        <el-table-column prop="state" :formatter="formatState" label="状态"></el-table-column>
        <el-table-column label="操作"  :formatter="formatState" width="150px">
          <template #default="{ row }">
            <el-button type="text"  @click="switchState(row.state, row.id)" value="row.state">{{ row.state === 0 ? '启用' : '禁用' }}</el-button>
            <el-button type="text" :disabled="row.state === 1" @click="showDialog(row)">修改</el-button>
            <el-button type="text" :disabled="row.state === 1" @click="delTabs(row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <div class="paging">
        <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[5, 10, 20, 40]"
          layout="prev, pager, next, sizes, jumper"
          :page-size="pagesize"
          :total="counts">
        </el-pagination>
      </div>
    </el-card>

    <TagsAdd :showTagsAdd.sync="showTagsAdd" :tabAdd="tabAdd" @submitTabs="submitTabs"></TagsAdd>
  </div>
</template>

<script>
import { list, changeState, remove } from '@/api/interview/tags'
import { status } from '@/api/interview/constants'
import TagsAdd from '../components/tags-add.vue'
export default {
  name: 'Tags',
  components: { TagsAdd },
  data () {
    return {
      formData: {
        tagName: null,
        state: null
      },
      showTagsAdd: false,
      pagesize: 10,
      currentPage: 1,
      counts: 0,
      tableData: [],
      tabAdd: {}
    }
  },
  created () {
    this.getTabsData()
  },
  computed: {
    subject () {
      return this.$route.query || {}
    }
  },
  watch: {
    '$route.query': function () {
      this.getTabsData()
    }
  },
  methods: {
    async serchTabs () { // 上方搜索框
      const { data } = await list({ ...this.formData, page: this.currentPage, pagesize: this.pagesize })
      this.tableData = data.items
    },
    clearTabs () { // 清除搜索
      this.formData = {
        tagName: null,
        state: null
      }
      this.getTabsData()
    },
    submitTabs () { // 确认修改后刷新
      this.getTabsData()
    },
    async getTabsData () { // 获取表格数据
      const { data } = await list({
        ...this.formData,
        subjectID: this.subject.id || null,
        page: this.currentPage,
        pagesize: this.pagesize
      })
      this.counts = data.counts
      this.tableData = data.items
    },
    handleSizeChange (currentSizePage) { // 改变每页有多少条数据
      this.pagesize = currentSizePage
      this.getTabsData()
    },
    handleCurrentChange (newPage) { // 直接去某一页
      this.currentPage = newPage
      this.getTabsData()
    },
    indexFn (index) { // 序号排序
      return index + 1 + this.pagesize * (this.currentPage - 1)
    },
    formatState (row, column, cellValue, index) { // 格式化状态
      // console.log(row)
      let res = ''
      status.forEach(item => {
        if (item.value === +cellValue) {
          res = item.label
        }
      })
      return res
    },
    async showDialog (row = {}) { // 开启对话框
      this.showTagsAdd = true
      // console.log(row)
      this.tabAdd = row
      // console.log(res)
    },
    async switchState (state, id) { // 切换启用禁用
      const newState = state === 1 ? 0 : 1
      await changeState({ id, state: newState })
      this.$message.success('切换成功')
      this.getTabsData()
    },
    async delTabs (id) { // 删除
      await this.$confirm('此操作将永久删除该标签, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      await remove({ id })
      this.$message.success('删除成功')
      // 如果删除的是本页的最后一条数据,且删除成功了 => 回到上一页
      if (this.tableData.length === 1 && this.currentPage > 1) {
        this.currentPage--
      }
      this.getTabsData()
    }
  }

}
</script>

<style scoped  lang='scss'>
.container {
  padding: 10px;
  ::v-deep .el-table__header {
    border-bottom: 1px solid #e8e8e8;
  }

  .paging {
    margin-top: 20px;
    margin-bottom: 20px;
    float: right;
  }
  ::v-deep .el-table th, .el-table tr {
    background-color: #fafafa;
  }
}
</style>
