<template>
  <div class="container-questions">
    <el-card>
      <!-- 第一行 -->
      <div class="add-button">
        <span class="tip">说明：目前支持学科和关键字条件筛选</span>
        <el-button type="success"
       @click=" $router.push('new')"
        >
          <i class="el-icon-edit"></i>
          新增试题</el-button>
      </div>
      <!-- 第二行 -->
      <div class="search">
        <div class="search-col">
          <span class="title" style="padding-left:30px">学科</span>
          <el-select clearable   placeholder="请选择" v-model="subject.value">
            <el-option v-for="item in subject "
            :key="item.value"
            :label="item.label"
            :value="item.value"
            @click.native="clickSubject(item.value)"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col">
          <span class="title">二级目录</span>
          <el-select clearable  placeholder="请选择"  v-model="subDirectories.value">
            <el-option v-for="item in subDirectories"
            :key="item.id"
            :label="item.label"
            :value="item.value"
            @click.native="clicksubDirectories(item.value)"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col">
          <span class="title">标签</span>
          <el-select clearable   placeholder="请选择" v-model="tags.value">
            <el-option v-for=" item in tags"
             :key="item.value"
            :label="item.label"
            :value="item.value"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col">
          <span class="title" >关键字</span>
          <el-input v-model="clave" placeholder="请输入内容"  class="inputSize" size="medium"></el-input>
        </div>
      </div>

      <!-- 第三行 -->
      <div class="search">
        <div class="search-col">
          <span class="title">试题类型</span>
          <el-select clearable   placeholder="请选择" v-model="questionType.label">
            <el-option v-for="item in questionType"
             :key="item.value"
            :label="item.label"
            :value="item.value"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col">
          <span class="title" style="padding-left:30px">难度</span>
          <el-select clearable  placeholder="请选择" v-model="difficulty.label">
            <el-option
            v-for="item in difficulty"
             :key="item.value"
            :label="item.label"
            :value="item.value"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col">
          <span class="title">方向</span>
          <el-select clearable  placeholder="请选择"  v-model="direction.index" >
            <el-option
              v-for="(item,index) in direction"
             :key="index"
            :label="item"
            :value="item"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col">
          <span class="title">录入人</span>
          <el-select clearable   placeholder="请选择" v-model="userInfo.username">
            <el-option
                 v-for="item in userInfo"
             :key="item.id"
            :label="item.username"
            :value="item.id"
            ></el-option>
          </el-select>
        </div>
      </div>

      <!-- 第四行 -->
      <div class="search">
         <div class="search-col">
          <span class="title" >题目备注</span>
          <el-input v-model="Comentarios" placeholder="请输入内容" class="inputSize" size="medium"></el-input>
        </div>
        <div class="search-col">
          <span class="title" >企业简称</span>
          <el-input v-model="negocio" placeholder="请输入内容" class="inputSize" size="medium"></el-input>
        </div>
        <div class="search-city">
          <span class="title" style="padding-top:8px">城市</span>
          <!-- 省 -->
          <el-select clearable  placeholder="请选择" size="small" style="width:100px"
          v-model="provincesOptions.value"
          >
            <el-option
            v-for="item in provincesOptions"
            :key="item"
            :label="item"
            :value="item"
            @click.native="handleCity(item)"
            ></el-option>
          </el-select>
          <!-- 市 -->
          <el-select clearable  class="country" placeholder="请选择" size="small"  style="width:100px"
          v-model="citysOptions.value"
          >
            <el-option
            v-for="item in citysOptions"
            :key="item"
            :label="item"
            :value="item"
            ></el-option>
          </el-select>
        </div>
        <div class="search-col  last-button">
         <div class="floatbutton">
            <el-button size="small"
          @click.native="handleClear"
          >清除</el-button>
          <el-button size="small" type="primary"
          @click.native="handleSearrch"
          >搜索</el-button>
         </div>
        </div>
      </div>

<!-- 消息提示带 -->
       <el-alert
       style="margin-top:20px"
     :title="`数据一共 ${counts} 条`"
    type="info"
     show-icon
    >
       </el-alert>

  <!-- 弹层组件 -->
  <QuestionsPreview ref="previewDialog" :rowsList="rowsList" :newQuestionType="newQuestionType"
  :newDifficulity="newDifficulity"></QuestionsPreview>
       <!-- 表格 -->
       <!--
         number:XXXX
         questionType:1
         difficulty:1
         videoURL:
         addDate:
         subject
        -->
      <el-table :data="questionList">
        <el-table-column label="试题编号" prop="number" weight="240"></el-table-column>
        <el-table-column label="学科" prop="subject"></el-table-column>
        <el-table-column label="目录" prop="catalog"></el-table-column>
        <el-table-column label="题型">
          <template #default="{row}">
            {{getAllQuestion(row.questionType)}}
            <!-- {{row.questionType}} -->
            <!-- {{questionType.find(item=>item.value=== +row.questionType).label}} -->
          </template>
        </el-table-column>
        <el-table-column label="题干" width="280px">
          <template #default="{ row }">
            <div v-html="row.question"></div>
          </template>
        </el-table-column>
        <el-table-column label="录入时间" width="180">
          <template #default="{ row }">
            {{row.addDate|parseTimeByString}}
          </template>
        </el-table-column>
        <el-table-column label="难度">
          <template #default='{row}'>
            {{getAlldifficulty(row.difficulty)}}
            <!-- {{difficulty.find(item=>item.value=== + row.difficulty).label}} -->
          </template>
        </el-table-column>
        <el-table-column label="录入人" prop="creator"></el-table-column>
        <el-table-column label="操作" width="180px">
          <template  #default={row}>
            <el-button type="primary" size="small" circle icon="el-icon-view" title="预览"
            @click="clcikPriview(row)"
            ></el-button>
            <el-button type="success" size="small" circle icon="el-icon-edit" title="修改"
            @click="$router.push(`new?id=${row.id}`)"
            ></el-button>
            <el-button type="danger" size="small" circle icon="el-icon-delete" title="删除"
            @click="del(row.id)"
            ></el-button>
            <el-button type="warning" size="small" circle icon="el-icon-check" title="加入精选"
            @click="handleAdd(row.id)"
            ></el-button>

          </template>
        </el-table-column>
      </el-table>

      <!-- 分页器 -->
      <div class="block">
        <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="page"
      :page-sizes="[5, 10, 15, 20,25]"
      :page-size="pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="counts">
    </el-pagination>
</div>
    </el-card>
  </div>
</template>

<script>
import { simple } from '@/api/interview/subjects.js'
import { simple as directorysimple } from '@/api/interview/directorys.js'
import { tagSimple } from '@/api/interview/tags.js'
import { difficulty, questionType, direction } from '@/api/interview/constants.js'
import { simple as getUserName } from '@/api/base/users.js'
import { provinces, citys } from '@/api/interview/citys.js'
import { list, remove, choiceAdd } from '@/api/interview/questions.js'
import QuestionsPreview from '../components/questions-preview.vue'
export default {
  name: 'Question',
  components: {
    QuestionsPreview
  },
  data () {
    return {
      subject: [{ // 学科
        value: '',
        label: ''
      }],
      subDirectories: [{ // 二级目录
        value: '',
        label: ''
      }],
      tags: [{ // 标签
        value: '',
        label: ''
      }],
      clave: '', // 关键字
      difficulty, // 难度
      questionType, // 试题类型
      direction,
      userInfo: [],
      Comentarios: '', // 题目备注
      negocio: '', // 企业简称
      provincesOptions: [], // 大市
      citysOptions: [], // 小市
      counts: 0, // 数据条目
      questionList: [], // 获取到的数据列表
      rowsList: {},
      newQuestionType: '',
      newDifficulity: '',
      page: 1,
      pagesize: 5,
      questionsTypeValue: '',
      difficultyTypeValue: ''

    }
  },
  // computed: {
  //   questionsTypeValue () {
  //     const questionValue = questionType.find(item => item.value === +this.rowsList.questionType)
  //     return questionValue ? questionValue.label : null
  //   },
  //   difficultyTypeValue () {
  //     const difficultyValue = difficulty.find(item => item.value === +this.rowsList.difficulty)
  //     return difficultyValue ? difficultyValue.label : null
  //   }
  // },
  methods: {
    async getSubject () { // 一级目录
      const { data } = await simple()
      this.subject = data
    },
    async clickSubject (subjectID) {
      const { data } = await directorysimple({ subjectID })
      // console.log(res)
      this.subDirectories = data
    },
    async clicksubDirectories (subjectID) {
      const { data } = await tagSimple(subjectID)
      this.tags = data
    },
    async getusers () { // 获取用户
      const { data } = await getUserName()
      this.userInfo = data
      // console.log(data)
    },
    handleCity (item) {
      this.citysOptions = citys(item)
    },
    handleClear () { // 清除按钮
      this.subject.value = ''
      this.subDirectories.value = ''
      this.tags.value = ''
      this.clave = ''
      this.difficulty.label = ''
      this.questionType.label = ''
      this.direction.value = ''
      this.userInfo.username = ''
      this.Comentarios = ''
      this.negocio = ''
      this.provincesOptions.value = ''
      this.citysOptions.value = ''
    },
    async handleSearrch () { // 搜索
      let subjectID = this.subject.value
      let catalogID = this.subDirectories.value
      let keyword = this.clave
      const page = this.page
      const pagesize = this.pagesize
      if (subjectID === undefined) {
        subjectID = null
      }
      if (catalogID === undefined) {
        catalogID = null
      }
      if (keyword === undefined) {
        keyword = null
      }
      const { data: questionOptions } = await list({
        subjectID,
        catalogID,
        keyword,
        pagesize,
        page
      })
      this.counts = questionOptions.counts
      this.questionList = questionOptions.items
      // console.log(this.questionList)
    },
    clcikPriview (row) { // 查看按钮
      this.$refs.previewDialog.openDialog()
      // console.log(row)
      this.rowsList = row
      this.newQuestionType = questionType.find(item => item.value === +row.questionType).label
      // console.log(this.newQuestionType)
      this.newDifficulity = difficulty.find(item => item.value === +row.difficulty).label
    },
    del (id) { // 删除按钮
      this.$confirm('确定要删除吗', '温馨提示', {
        type: 'warning'
      }).then(async () => {
        await remove({ id })
        // console.log(res)
        this.$message.success('删除成功')
        this.handleSearrch()
      }).catch((err) => {
        console.log(err)
      })
    },
    handleAdd (id) {
      this.$confirm('此操作将该题目加入精选，是否继续？', '提示', {
        type: 'info'
      }).then(async () => {
        await choiceAdd({ id, choiceState: 1 })
        // console.log(res)
        this.$message.success('加入成功')
        this.handleSearrch()
      }).catch((err) => {
        console.log(err)
      })
    },
    handleSizeChange (val) {
      this.pagesize = val
      this.page = 1
      this.handleSearrch()
    },
    handleCurrentChange (val) {
      this.page = val
      this.handleSearrch()
    },
    // async getbaseList () {
    //   const { data: questionOptions } = await list({
    //     page: 1,
    //     pagesize: 5
    //   })
    //   this.counts = questionOptions.items.length
    //   this.questionList = questionOptions.items
    //   console.log(this.questionList)
    // },
    getAllQuestion (type) {
      // this.difficultyTypeValue = difficulty.find(item => item.value === +this.rowsList.difficulty).label
      const questionValue = questionType.find(item => item.value === +type)
      return questionValue ? questionValue.label : null
    },
    getAlldifficulty (type) {
      const difficultyValue = difficulty.find(item => item.value === +type)
      return difficultyValue ? difficultyValue.label : null
    }
  },
  created () {
    this.getSubject()
    this.getusers()
    this.provincesOptions = provinces()
    // this.getbaseList()
    this.handleSearrch()
  }
}

</script>

<style scoped lang='scss'>
.inputSize{
  width: 217px;
}
.block{
  float: right;
}
.add-button {
  display: flex !important;
  justify-content: space-between;
}
.tip {
  font-size: 12px;
  color: pink;
}
.title {
  display: inline-block;
  margin-right: 12px;
  white-space: nowrap;
}
.search {
display: flex;
  margin-top: 20px;
  font-size: 14px;
  margin-right: 40px;
}
.search-col {
flex: 1;
display: flex;
flex-wrap: nowrap !important;
align-items: center;
}
.search-city{
  flex: 1;
  display: flex;
}
.country{
  margin-left: 10px !important;
}
.floatbutton{
  float: right !important;
  margin-left: 150px;
}

</style>
