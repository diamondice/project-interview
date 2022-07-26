<template>
  <div class='container-questionNew'>
    <el-card class="questionNew">
<div class="title">
{{title}}
</div>
    <el-divider></el-divider>

    <!-- form表单 -->
    <el-form :model="form" :rules="rules" ref="form" class="fromrCss">
    <!-- 学科 -->
      <el-form-item label="学科:" prop="subjectID">
  <el-select v-model="form.subjectID">
    <el-option
     v-for="item in subjectList"
      :key="item.value"
      :label="item.label"
      :value="item.value"
      @click.native="handleSubject(form.subjectID)"
    ></el-option>
  </el-select>
      </el-form-item>

      <!-- 目录 -->
      <el-form-item label="目录:"  prop="catalogID">
        <el-select v-model="form.catalogID">
          <el-option
          v-for="item in directorysList"
          :key="item.value"
          :value="item.value"
          :label="item.label"
          >

          </el-option>
        </el-select>
      </el-form-item>

      <!-- 企业 -->
      <el-form-item label="企业:"  prop="enterpriseID">
<el-select v-model="form.enterpriseID">
  <el-option
  v-for="item in companyList"
  :key="item.number"
  :label="item.company"
  :value="item.id"
  >
  </el-option>
</el-select>
      </el-form-item>

      <!-- 城市 -->
      <el-form-item label="城市:" prop="city">
        <el-select
        v-model="form.province"
        >
        <el-option
         @click.native="handlecitys(form.province)"
        v-for="item in provincesOptions"
        :key="item"
        :value="item"
        :label="item"
        ></el-option>
        </el-select>
        <el-select style="margin-left:10px" v-model="form.city"
        >
          <el-option
          v-for="item in citysList"
          :key="item"
          :value="item"
          :label="item"
          ></el-option>
        </el-select>
      </el-form-item>

      <!-- 方向 -->
      <el-form-item label="方向:" prop="direction">
        <el-select v-model="form.direction"  @click.native="getdirection">
          <el-option
          v-for="item in direction"
          :key="item"
          :value="item"
          :label="item"
          ></el-option>
        </el-select>
      </el-form-item>

      <!-- 题型 -->
       <el-form-item label="题型：" prop="questionType">
            <el-radio-group @change="changeRadio({})" v-model="form.questionType">
              <el-radio v-for="item in questionType" :key="item.value" :label="item.value+''">{{item.label}}</el-radio>
            </el-radio-group>
         </el-form-item>

        <!-- 难度 -->
        <el-form-item label="难度：" prop="difficulty">
            <el-radio-group v-model="form.difficulty">
              <el-radio v-for="item in difficulty" :key="item.value" :label="item.value+''">{{item.label}}</el-radio>
            </el-radio-group>
         </el-form-item>

      <!-- 题干(富文本编辑器) -->
      <el-form-item label="题干:" prop="question">
        <!-- <quill-editor class="editor"
                    ref="myTextEditor"
                    v-model="content"
                    :options="editorOption"
                    @blur="onEditorBlur($event)"
                    @focus="onEditorFocus($event)"
                    @ready="onEditorReady($event)"
                    @change="onEditorChange($event)">
      </quill-editor> -->
      <quill-editor class="editor"
                    ref="myTextEditor"
                    v-model="form.question"
                    :options="editorOption"
                    @blur="checkQustion"
                    @change="onEditorChange($event)">
      </quill-editor>
      </el-form-item>

      <!-- 选项 -->
      <el-form-item label="选项："
       prop="options"
      v-if="form.questionType!=='3'" >
          <div v-for="item in form.options" :key="item.code" style="margin:40px 0 0 90px" >
            <el-radio v-if="form.questionType==='1'" @change="changeRadio(item)" v-model="item.isRight" :label="true">{{item.code}}：</el-radio>
            <div class="uploadBox">
  <el-upload
     class="avatar-uploader"
     action="#"
      v-for="item in form.options" :key="item.code"
  >
     上传图片
  <div class="el-icon-circle-close close-icon"></div>
  </el-upload>
</div>
            <el-checkbox v-if="form.questionType==='2'" v-model="item.isRight" :label="true">{{item.code}}：</el-checkbox>
            <el-input v-model="item.title"  style="width:250px"></el-input>

            </div>

             <!-- 添加按钮 -->
          <el-button type="danger" style="margin:20px 0 0 90px"
          :disabled="form.questionType!=='2'"
          @click="addOption"
          size="small">+增加选项与答案</el-button>
      </el-form-item>

       <!-- 视频 -->
        <el-form-item label="解析视频：" prop="videoURL">
          <el-input style="width:400px" v-model="form.videoURL" ></el-input>
        </el-form-item>

        <!-- 答案解析 -->
        <el-form-item label="答案解析：" prop="answer">
            <quill-editor class="editor"
             ref="myTextEditor2"
                    v-model="form.answer"
                    :options="editorOption"
                    @blur="checkQustion2"
                    @change="onEditorChange2($event)">
      </quill-editor>
        </el-form-item>

      <!-- 题目备注-->
        <div class="remarks" prop="remarks">
          <el-form-item label="题目备注：" >
          <el-input
          style="width:400px"
          rows=4
          type="textarea"  v-model="form.remarks"></el-input>
        </el-form-item>
        </div>

        <!-- 试题标签 -->
        <el-form-item label="试题标签">
          <el-select v-model="form.tags" multiple>
            <el-option
                v-for="item in tagsOptions"
                :key="item.value"
                :label="item.label"
                :value="item.label">
            </el-option>
          </el-select>
        </el-form-item>

        <!-- 提交 -->
         <!-- 提交按钮-->
        <el-form-item>
          <el-button v-if="!$route.query.id"
          @click="submit" type="primary">确认提交</el-button>
          <el-button v-else @click="update" type="success">确认修改</el-button>
        </el-form-item>
    </el-form>
    </el-card>
  </div>
</template>

<script>
import { simple } from '@/api/interview/subjects.js'
import { simple as getDirectorys } from '@/api/interview/directorys.js'
import { list as getCompany } from '@/api/interview/companys.js'
import { provinces, citys } from '@/api/interview/citys.js'
import { direction, questionType, difficulty } from '@/api/interview/constants.js'
import { tagSimple as tagssimple } from '@/api/interview/tags.js'
import { add, detail, update } from '@/api/interview/questions'
export default {
  data () {
    return {
      form: {
        subjectID: null,
        catalogID: null,
        enterpriseID: null,
        province: null,
        city: null,
        direction: null,
        questionType: '1',
        difficulty: '1',
        question: null,
        options: [
          { isRight: false, code: 'A', title: '', img: '' },
          { isRight: false, code: 'B', title: '', img: '' },
          { isRight: false, code: 'C', title: '', img: '' },
          { isRight: false, code: 'D', title: '', img: '' }
        ],
        remarks: null,
        tags: null
      },
      editorOption: { // 富文本编辑器
        modules: {
          toolbar: [
            ['bold', 'italic', 'underline', 'strike'], // 加粗 斜体 下划线 删除线
            // [{ header: 1 }, { header: 2 }], // 1、2 级标题
            [{ list: 'ordered' }, { list: 'bullet' }], // 有序、无序列表
            ['blockquote', 'code-block'], // 引用  代码块
            // [{ script: 'sub' }, { script: 'super' }], // 上标/下标
            // [{ indent: '-1' }, { indent: '+1' }], // 缩进
            // [{'direction': 'rtl'}],                         // 文本方向
            // [{ size: ['small', false, 'large', 'huge'] }], // 字体大小
            // [{ header: [1, 2, 3, 4, 5, 6, false] }], // 标题
            // [{ color: [] }, { background: [] }], // 字体颜色、字体背景颜色
            // [{ font: [] }], // 字体种类
            // [{ align: [] }], // 对齐方式
            // ['clean'], // 清除文本格式
            ['image', 'link'] // 链接、图片、视频
          ] // 工具菜单栏配置
        },
        placeholder: '', // 提示
        readyOnly: false, // 是否只读
        theme: 'snow', // 主题 snow/bubble
        syntax: true // 语法检测
      },
      subjectList: [], // 一级科目
      directorysList: [], // 二级目录
      companyList: {}, // 公司
      provincesOptions: [], // 市
      citysList: [], // 二级市
      direction: [], // 方向
      questionType,
      difficulty,
      tagsOptions: [], // 标签
      rules: { // 校验规则
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'change' }
        ],
        catalogID: [
          { required: true, message: '请选择目录', trigger: 'change' }
        ],
        enterpriseID: [
          { required: true, message: '请选择企业', trigger: 'change' }
        ],
        city: [
          { required: true, message: '请选择地区', trigger: 'change' }
        ],
        direction: [
          { required: true, message: '请选择方向', trigger: 'change' }
        ],
        questionType: [
          { required: true, message: '请选择题型', trigger: 'change' }
        ],
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'change' }
        ],
        question: [
          { required: true, message: '请输入题干', trigger: 'blur' }
        ],
        options: [
          { required: true, message: '请至少选择一个选项', trigger: 'change' }
        ],
        answer: [
          { required: true, message: '请输入答案解析', trigger: 'change' }
        ]
      }
    }
  },
  created () {
    this.getSubects()
    this.getCompanys()
    this.provincesOptions = provinces()
    if (this.$route.query.id) {
      this.getDetaioList()
    }
  },
  watch: {
    '$route.query': function () {
      if (this.$route.query.id) {
        this.getDetaioList()
      } else {
        this.form = {
          subjectID: null,
          catalogID: null,
          enterpriseID: null,
          province: null,
          city: null,
          direction: null,
          questionType: '1',
          difficulty: '1',
          question: null,
          options: [
            { isRight: false, code: 'A', title: '', img: '' },
            { isRight: false, code: 'B', title: '', img: '' },
            { isRight: false, code: 'C', title: '', img: '' },
            { isRight: false, code: 'D', title: '', img: '' }
          ],
          remarks: null,
          tags: null
        }
        this.$nextTick(() => {
          this.$refs.form.clearValidate()
        })
      }
    }
  },
  computed: {
    title () {
      return this.$route.query.id ? '试题修改' : '试题录入'
    }
  },
  methods: {
    async getDetaioList () {
      const { data } = await detail({ id: this.$route.query.id })
      // console.log(data)
      data.tags = data.tags.split(',')
      data.options = data.options.map(item => {
        item.isRight = item.isRight === 1
        return item
      })
      this.form = data
      const res = await getDirectorys({ subjectID: this.form.subjectID })
      this.directorysList = res.data
      const res2 = await tagssimple({ subjectID: this.form.subjectID })
      this.tagsOptions = res2.data
      // 滚动顶部
      this.$nextTick(() => {
        window.scrollTo(0, 0)
      })
    },
    async  getSubects () { // 获取学科
      const { data } = await simple()
      this.subjectList = data
    },
    async handleSubject (id) { // 获取二级目录
      // console.log(id)
      const { data } = await getDirectorys(id)
      this.directorysList = data
      const { data: res } = await tagssimple(id)
      this.tagsOptions = res
      // console.log(this.tagsOptions)
    },
    async getCompanys () { // 获取企业
      const { data } = await getCompany({ pagesize: 10000 })
      this.companyList = data.items
      console.log(this.companyList)
    },
    handlecitys (id) { // 获取城市
      // console.log(this.provincesOptions)
      // console.log(id)
      this.citysList = citys(id)
    },
    getdirection () { // 获取方向
      this.direction = direction
      // console.log(this.direction)
    },
    checkQustion () { // 失去焦点
      // 调用el-form组件的 validateField 校验  question  字段
      // this.$refs.form.validateField('question')
    },
    checkQustion2 () {
      // 调用el-form组件的 validateField 校验  question  字段
      // this.$refs.form.validateField('question
    },
    onEditorChange (editor) { // 值发生变化
      this.form.question = editor.html
      // console.log(editor)
    },
    onEditorChange2 (editor) { // 值发生变化
      this.form.answer = editor.html
      // console.log(editor)
    },
    changeRadio (item) {
      // 1. 清除所有的选中
      this.form.options.forEach(item => {
        item.isRight = false
      })
      // 2. 自己选中
      item.isRight = true
    },
    addOption () {
      this.form.options.push({
        isRight: false,
        code: String.fromCharCode(65 + this.form.options.length),
        title: '',
        img: ''
      })
    },
    submit () { // 提交
      this.$refs.form.validate(async (flag) => {
        if (!flag) return
        // const res = await add(this.form)
        // console.log(this.form)
        const data = { ...this.form }
        data.tags = data.tags.join(',')
        await add(data)
        this.$message.success('添加成功')
        this.$router.push('/questions/list')
      })
    },
    update () { // 修改
      this.$refs.form.validate(async valid => {
        if (valid) {
          const data = { ...this.form }
          data.tags = data.tags.join(',')
          await update(this.form)
          this.$message.success('修改成功')
          this.$router.push('/questions/list')
        }
      })
    }
  }
}
</script>

<style scoped lang='scss'>
.title{
  font-size: 16px;
 padding-left: 10px;
 padding-top: 10px;
}
.questionNew{
  margin: 10px 10px ;
}
.editor{
  margin-left: 70px;
  margin-right: 20px;
  // height: 180px;
}
.fromrCss{
  padding-left: 50px;
}
.remarks{
  margin-top: 80px;
}
::v-deep{
  .el-form-item__error{
  margin-left: 56px;
}
.ql-editor{
  height: 180px;
}
.avatar-uploader  {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    width: 100px;
    height: 50px;
    text-align: center;
    line-height: 60px;
    margin-bottom: 30px;
    cursor: pointer;
    overflow: hidden;
    color: black !important;
    font-size: 14px;
    margin-left: 100px;
    margin-top: 32px;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
   .avatar-uploader:hover {
    border-color: #409EFF;
  }
.uploadBox{
    position: absolute;
    top: -10px;
    left: 320px;
}
.close-icon {
  position: absolute;
  margin-top: -5px;
  background: #fff;
  margin-left: 15px;
}
}
</style>
