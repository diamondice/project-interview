<template>
  <div class='container'>
    <el-dialog title="题目预览" :visible="showDialog" @close="handleClose" @open="preview"  v-loading="loading">
      <el-row>
        <el-col :span="6">【题型】：{{ perviewData.questionType|tanslateType}}</el-col>
        <el-col :span="6">【编号】：{{perviewData.id}}</el-col>
        <el-col :span="6">【难度】：{{perviewData.difficulty}}</el-col>
        <el-col :span="6">【标签】：{{perviewData.tags}}</el-col>
        <el-col :span="6">【学科】：{{perviewData.subjectID}}</el-col>
        <el-col :span="6">【目录】：{{perviewData.catalogID}}</el-col>
        <el-col :span="6">【方向】：{{perviewData.direction}}</el-col>
      </el-row>
      <hr>

      <el-row>【题干】：</el-row>
      <el-row>{{perviewData.question|html2Text}}</el-row>
<!-- 只有此处结构不同 -->
 <!-- 单选题 -->
      <div v-if="perviewData.questionType==='1'">
        <el-row>
          <el-col :span="4">单选题型 选项</el-col>
          <el-col :span="20">(以下选中的选项为正确答案)</el-col>
        </el-row>
        <el-radio-group v-model="radio">
          <div style="margin-bottom:5px" v-for="item in perviewData.options" :key="item.id">
            <el-radio :label="item.code">{{item.title}}</el-radio>
          </div>
        </el-radio-group>
      </div>
   <!-- 多选题 -->
      <div v-else-if="perviewData.questionType==='2'">
        <el-row>
          <el-col :span="4">多选题型 选项</el-col>
          <el-col :span="20">(以下选中的选项为正确答案)</el-col>
        </el-row>
        <el-checkbox-group v-model="checkList">
          <div style="margin-bottom:5px" v-for="item in perviewData.options" :key="item.id">
            <el-checkbox :label="item.code">{{item.title}}</el-checkbox>
          </div>
        </el-checkbox-group>
      </div>
  <!-- 简答 -->
<!-- 只有此处结构不同 -->
     <hr>
      <el-row type="flex" align="middle">
        <el-col :span="4">【参考答案】:</el-col>
        <el-col :span="20">
          <el-button type="danger" @click="videoShow=!videoShow">视频答案预览</el-button>
        </el-col>
      </el-row>
        <div v-show="videoShow">
          <video src="perviewData.videoURL" controls width="300"></video>
        </div>
      <hr>
      <el-row class="el-row" type="flex" align="middle">
        <el-col :span="4">【答案解析】:</el-col>
        <el-col :span="20">{{perviewData.answer|html2Text}}</el-col>
      </el-row>
      <hr>
      <el-row class="el-row" type="flex" align="middle">
        <el-col :span="4">【题目备注】:</el-col>
        <el-col :span="20">{{perviewData.remarks}}</el-col>
      </el-row>
      <template #footer>
        <el-button type="primary" @click="handleClose">关闭</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script>
// import { translateType } from '../../filters/index'
import { detail } from '../../api/interview/questions'
export default {
  name: 'QuestionPerview',
  props: {
    showDialog: {
      type: Boolean,
      default: false
    },
    perviewId: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      perviewData: {},
      videoShow: false,
      loading: false
      // questionType: '2',
      // radio: 'A',
      // checkList: []
    }
  },
  computed: {
    radio: {
      get () {
        return this.perviewData.options.find(item => item.isRight === 1).code
      },
      set () {

      }
    },
    checkList: {
      get () {
        return this.perviewData.options.filter(item => item.isRight).map(item => item.code)
      },
      set () {

      }
    }
  },
  methods: {
    handleClose () {
      this.$emit('update:showDialog', false)
    },
    async preview () {
      this.loading = true
      const { data } = await detail({ id: this.perviewId })
      // console.log(data)
      this.perviewData = data
      this.loading = false
    }
  }
}
</script>

<style scoped lang='scss'>
 .el-row {
   margin: 10px 0;

   .el-col-6{
     margin: 10px 0;
   }
 }
 .el-radio {
   margin-bottom: 5px;
 }

</style>
