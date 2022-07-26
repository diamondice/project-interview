<template>
  <div class='container'>
    <el-card>
      <div class="top">
        <div class="left">
          <el-form ref="form"  :model="requestList" :inline="true">
            <el-form-item label="学科名称" prop="subjectName">
              <el-input v-model="requestList.subjectName" ></el-input>
            </el-form-item>
          </el-form>
          <div class="button">
            <el-button size="small" @click="requestList.subjectName = '' ">清除</el-button>
            <el-button type="primary" size="small" @click="searchSubject">搜索</el-button>
          </div>
        </div>
        <div class="right">
          <el-button type="success" size="small" @click="addShow">新增学科</el-button>
        </div>
      </div>
      <el-alert
    :title="`数据一共${total}条`"
    type="info"
    :closable="false"
    show-icon>
  </el-alert>
      <el-table :data="subjectList"  v-loading="loading">
        <el-table-column label="序号" type="index" :index="indexFn" ></el-table-column>
        <el-table-column label="学科名称" prop="subjectName" ></el-table-column>
        <el-table-column label="创建者" prop="username" ></el-table-column>
        <el-table-column label="创建日期" prop="addDate" >
          <template #default="{row}">
            {{row.addDate | parseTimeByString()}}
          </template>
        </el-table-column>
        <el-table-column label="前台是否显示" prop="isFrontDisplay" :formatter="formatterFn"></el-table-column>
        <el-table-column label="二级目录" prop="twoLevelDirectory" ></el-table-column>
        <el-table-column label="标签" prop="tags" ></el-table-column>
        <el-table-column label="题目数量" prop="totals" ></el-table-column>
        <el-table-column label="操作" width="250px">
        <template #default="{row}">
        <el-button type="text" size="medium" @click="$router.push('/subjects/directorys')" >学科分类</el-button>
        <el-button type="text" size="medium" @click="$router.push('/subjects/tags')">学科标签</el-button>
        <el-button type="text" size="medium" @click="amend(row.id)">修改</el-button>
        <el-button type="text" size="medium" @click="del(row.id)">删除</el-button>
        </template>
        </el-table-column>
      </el-table>
      <div class="page">
        <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="requestList.page"
        :page-size="requestList.pagesize"
        :total="total"
        :page-sizes="[1,5,8,10,12]"
        background
        layout="total, sizes, prev, pager, next, jumper"
        ></el-pagination>
      </div>
    </el-card>
    <AddSubject :show.sync="show" @add="add" :ruleForm="ruleForm"></AddSubject>
  </div>
</template>

<script>
import { list, remove, detail } from '@/api/interview/subjects.js'
import AddSubject from '../components/subjects-add.vue'
export default {
  components: {
    AddSubject
  },
  data () {
    return {
      loading: false,
      subjectName: '',
      subjectList: [],
      total: 20,
      requestList: {
        page: 1,
        pagesize: 10,
        subjectName: ''
      },
      ruleForm: {
        subjectName: '',
        isFrontDisplay: 1
      },
      show: false
    }
  },
  created () {
    this.getSubjectList()
  },
  methods: {
    async getSubjectList () {
      this.loading = true
      const { data } = await list(this.requestList)
      console.log(data)
      this.subjectList = data.items
      this.requestList.page = +data.page
      // this.requestList.pagesize = +data.pages
      this.total = data.counts
      this.loading = false
    },
    handleCurrentChange (val) {
      this.requestList.page = val
      this.getSubjectList()
    },
    handleSizeChange (val) {
      this.requestList.pagesize = val
      this.requestList.page = 1
      this.getSubjectList()
    },
    indexFn (index) {
      return index + 1 + this.requestList.pagesize * (this.requestList.page - 1)
    },
    addShow () {
      this.show = true
    },
    formatterFn (row, column, cellValue, index) {
      return cellValue === 1 ? '是' : '否'
    },
    add () {
      this.getSubjectList()
    },
    del (id) {
      this.$confirm('确定删除该学科吗，温馨提示').then(async () => {
        const res = await remove(id)
        console.log(res)
        this.$message.success('删除学科成功')
        if (this.subjectList.length === 1 && this.requestList.page > 1) {
          this.requestList.page--
        }
        this.getSubjectList()
      }).catch((err) => {
        console.log(err)
      })
    },
    async amend (id) {
      const { data } = await detail(id)
      this.ruleForm = data
      this.show = true
    },
    searchSubject () {
      // const res = await simple(this.subjectName)
      // console.log(res)
      this.requestList.page = 1
      this.getSubjectList(this.requestList)
    }
  }

}
</script>

<style scoped lang='scss'>
.container {
    // box-sizing: border-box;

  .top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    // margin-bottom: 20px;
    // box-sizing: border-box;
    .left {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 14px;
    // box-sizing: border-box;

      .button {
        margin-left: 8px;
        position: relative;
        bottom: 10px;
      }
      // .el-form {
      //   margin-top: 18px;
      // }
    }
    .right {
      position: relative;
        bottom: 10px;
    }
  }
  .hint {
    padding: 8px  16px;
    box-sizing: border-box;
    border-radius: 4px;
    background-color: #f4f4f5;
    font-size: 14px;
    color: #909399;
    i {
      margin-right: 10px;
    }
  }
  .page {
    display: flex;
    justify-content: end;
  }
}
</style>
