<template>
  <div  >
    <el-dialog
     class='containerPreview'
    title="预览"
    :visible="showDialong"
     @close="closeDialog"
    >
 <el-row :gutter="20">
  <el-col :span="6"><div class="grid-content bg-purple">【题型】：{{newQuestionType}}</div></el-col>
  <el-col :span="6"><div class="grid-content bg-purple">【编号】：{{rowsList.number}}</div></el-col>
  <el-col :span="6"><div class="grid-content bg-purple">【难度】：{{newDifficulity}}</div></el-col>
  <el-col :span="6"><div class="grid-content bg-purple">【标签】：{{rowsList.tags}}</div></el-col>
</el-row>
<el-row :gutter="20">
  <el-col :span="8"><div class="grid-content bg-purple">【学科】：{{rowsList.subject}}</div></el-col>
  <el-col :span="8"><div class="grid-content bg-purple">【目录】：{{rowsList.catalog}}</div></el-col>
  <el-col :span="8"><div class="grid-content bg-purple">【方向】：{{rowsList.direction}}</div></el-col>
</el-row>
        <!-- 分割线 -->
    <el-divider></el-divider>

<el-row :gutter="20">
  <el-col :span="24"><div class="grid-content bg-purple">【题干】：
    <p v-html="rowsList.question"></p>
    </div></el-col>
</el-row>

<el-divider></el-divider>

<el-row :gutter="20">
  <el-col :span="6"><div class="grid-content bg-purple">【参考答案】：
    <p v-html="rowsList.answer"></p>
    </div></el-col>
    <el-col :span="8"><div class="grid-content bg-purple">
         <el-button type="danger"
         @click.native="clickVideo = true "
         >视频答案预览</el-button>
         <video controls width="250" v-if="clickVideo" autoplay>
          <source src="rowsList.videoURL"
           type="video/webm">
            <source src="rowsList.videoURL"
           type="video/mp4">

</video>
      </div></el-col>
</el-row>

<el-divider></el-divider>

<el-row :gutter="20">
  <el-col :span="24"><div class="grid-content bg-purple">【答案解析】：
    <p v-html="rowsList.answer"></p>
    </div></el-col>
</el-row>

  <el-divider></el-divider>

<el-row :gutter="20">
  <el-col :span="24"><div class="grid-content bg-purple">【题目备注】：
    {{rowsList.remarks}}
    </div></el-col>
</el-row>
    </el-dialog>
  </div>
</template>

<script>
import { questionType } from '@/api/interview/constants.js'

export default {
  props: {
    rowsList: {
      type: Object,
      required: true
    },
    newQuestionType: {
      type: String
    },
    newDifficulity: {
      type: String
    }
  },
  // computed: {
  //   newQuestionType () {
  //     return this.questionType.find(item => item.value === +this.rowsList.questionType).label
  //   }
  // },
  data () {
    return {
      showDialong: false,
      questionType,
      clickVideo: false
    }
  },
  methods: {
    closeDialog () {
      this.showDialong = false
      this.clickVideo = false
    },
    openDialog () {
      this.showDialong = true
    }
  }
}
</script>

<style scoped lang='scss'>
.containerPreview{
height: 800px !important;
}
  .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    border-radius: 4px;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    // background-color: #f9fafc;
  }
</style>
