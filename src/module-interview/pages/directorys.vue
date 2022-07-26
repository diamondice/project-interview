<template>
  <div class='container'>

    <el-card class="card">
      <!--  breadcrumb-->
        <div slot="header" v-if="subject.name && subject.id">
           <el-breadcrumb separator-class="el-icon-arrow-right" >
            <el-breadcrumb-item :to="{ path: '/subjects/list' }">学科管理</el-breadcrumb-item>
            <el-breadcrumb-item>{{subject.name}}</el-breadcrumb-item>
            <el-breadcrumb-item>目录管理</el-breadcrumb-item>
          </el-breadcrumb>
        </div>

    <!-- 目录管理 -->
      <el-form :inline="true" ref="formList" :model="reqParams">
        <el-form-item label="目录名称" class="item-list">
          <el-input v-model="reqParams.directoryName"></el-input>
        </el-form-item>
        <el-form-item label="状态" >
          <el-select v-model="reqParams.state" placeholder="请选择">
            <el-option label="启用" :value="1"></el-option>
            <el-option label="禁用" :value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item style="text-align:right">
          <el-button @click="clear()">清除</el-button>
          <el-button type="primary" @click="filter">搜索</el-button>
        </el-form-item>
        <el-form-item class="filter-item fr">
          <el-button type="text" icon="el-icon-back"
           @click="btn('/subjects/list','list')"
            v-if="subject.name && subject.id">返回学科</el-button>

          <el-button type="success"
          icon="el-icon-edit"
           @click="onCreateDirect">新增目录</el-button>
        </el-form-item>

      </el-form>

      <el-alert type="info"
      :title="`数据一共 ${total} 条`"
      :closable="false"
      show-icon
      ></el-alert>
      <!-- 表格 -->
      <el-table :data="direcList" style="width: 100%;margin-top: 10px">
        <el-table-column type="index" label="序号"></el-table-column>
        <el-table-column prop="subjectName" label="所属学科"></el-table-column>
        <el-table-column prop="directoryName" label="目录名称"></el-table-column>
        <el-table-column prop="username" label="创建者"></el-table-column>
        <el-table-column label="创建日期">
          <template #default="{row}">{{row.addDate|parseTimeByString}}</template>
        </el-table-column>
        <el-table-column prop="totals" label="面试题数量"></el-table-column>
        <el-table-column prop="state" label="状态">
          <template #default="{row}">{{row.state===1 ? '已启用' : '已禁用'}}</template>
        </el-table-column>
        <el-table-column label="操作">
            <template #default="{row}">
              <el-button type="text" @click="switchState(row)">{{row.state===1 ? '禁用' : '启用'}}</el-button>
              <el-button type="text" :disabled="row.state===1" @click="onEdit(row)" >修改</el-button>
              <el-button type="text" @click="del(row)" :disabled="row.state===1||row.totals > 0">删除</el-button>
            </template>
        </el-table-column>

      </el-table>
       <!-- 分页组件 -->
       <div style="text-align:right; margin-top: 10px" >
          <el-pagination
            :current-page="reqParams.page"
            :page-sizes="[1, 2, 3, 4,5,6,7,8,9,10]"
            :page-size="reqParams.pagesize"
            layout=" prev, pager, next, sizes, jumper"
            :total="total"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
          />
       </div>
  </el-card>
  <!-- 新增目录的对话框 -->
  <DirectorysAdd
  :showDialog.sync="showDialog"
  @add-directory="getList"
  :currDirectory="currDirectory">
  </DirectorysAdd>

  </div>

</template>

<script>
import { list, remove, changeState } from '@/api/interview/directorys'
import DirectorysAdd from '@/module-interview/components/directorys-add'
export default {
  components: {
    DirectorysAdd
  },
  data () {
    return {
      direcList: [],
      total: 0,
      currDirectory: {},
      reqParams: {
        directoryName: '',
        status: '',
        page: 1,
        pagesize: 10
      },
      showDialog: false

    }
  },
  created () {
    this.getList()
  },
  computed: {
    subject () {
      return this.$route.query || {}
    }
  },
  methods: {
    onCreateDirect () {
      this.showDialog = true
      this.currDirectory = {}
    },
    onEdit (clickedOne) {
      this.showDialog = true
      this.currDirectory = clickedOne
    },
    async switchState (directory) {
      await changeState({
        id: directory.id,
        state: directory.state === 1 ? 0 : 1
      })
      // console.log(res, 9999)
      directory.state = directory.state === 1 ? 0 : 1
      this.$message.success('操作成功')
    },
    async getList () {
      this.reqParams.subjectID = this.subject.id || null
      const { data } = await list(this.reqParams)
      // console.log(res)
      // console.log(data, 2222)
      this.direcList = data.items
      this.total = data.counts
    },
    handleSizeChange (val) {
      // console.log(`每页 ${val} 条`)

      this.reqParams.pagesize = val
      this.getList()
    },
    handleCurrentChange (val) {
      // console.log(`当前页: ${val}`)
      this.reqParams.page = val
      this.getList()
    },
    btn () {
      // 方式1: path跳转
      this.$router.push({
        path: '/subjects/list'
      })
    },
    clear () {
      this.reqParams = {
        directoryName: '',
        status: '',
        page: 1,
        pagesize: 10
      }
    },
    filter () {
      this.reqParams.page = 1
      this.getList()
    },
    async del (row) {
      await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      await remove(row)
      this.$message.success('删除成功')
      this.getList()
    }

  }
}
</script>

<style scoped lang="scss">
.card{
  margin:15px 10px ;
  ::v-deep{
      .el-breadcrumb__inner{
      color: #000;
      margin-bottom: 10px;
    }
    .item-list{
      margin-right: 40px;
    }
    .el-table__header{
      border-bottom:2px solid #ebeef5 ;
    }
  }

    ::v-deep{
      .el-table th {
        background-color: #fafafa;
    }
 }

}
</style>
