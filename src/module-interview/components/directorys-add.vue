<template>
  <div class='container'>
    <!-- 添加目录对话框 -->
    <el-dialog :title="`${currDirectory.id?'修改':'新增'}目录`"
      :visible="showDialog"
      @close="handleClose"
      width="500px"
      >
      <el-form ref="form" :rules="rules" :model="ruleForm" >
        <el-form-item label="所属学科"  v-if="!$route.query.id" prop="subjectID">
          <el-select v-model="ruleForm.subjectID" style="width:80%">
            <el-option
             v-for="item in subjectOptions"
             :key="item.value"
             :value="item.value"
             :label="item.label"
             >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录名称" prop="directoryName">
          <el-input v-model="ruleForm.directoryName" placeholder="请输入目录名称" style="width:80%">
          </el-input>
        </el-form-item>
      </el-form>
       <template #footer class="dialog-footer">
          <el-button @click="handleClose">取 消</el-button>
          <el-button type="primary" @click="clickSubmit">确 定</el-button>
        </template>
    </el-dialog>

  </div>
</template>

<script>
import { add } from '@/api/interview/directorys'
import { simple as subjectList } from '@/api/interview/subjects'
export default {
  props: {
    currDirectory: {
      default: () => {},
      type: Object
    },
    showDialog: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    showDialog (v) {
      if (v) {
        this.ruleForm.directoryName = this.currDirectory.directoryName
        this.ruleForm.subjectID = this.currDirectory.subjectID
      }
    }
  },
  data () {
    return {
      ruleForm: {
        subjectID: '',
        directoryName: ''
      },
      subjectOptions: [],

      rules: {
        subjectID: [
          { required: true, message: '请选择', trigger: ['change', 'blur'] }
        ],
        directoryName: [
          { required: true, message: '请输入目录名称', trigger: ['change', 'blur'] }
        ]
      }
    }
  },
  async created () {
    const { data: subjectArr } = await subjectList()
    this.subjectOptions = subjectArr
    // console.log(subjectArr)
  },
  methods: {
    handleClose () {
      this.$emit('update:showDialog', false)
      this.$refs.form.resetFields()
    },
    clickSubmit () {
      // this.$emit('update:showDialog', false)
      this.$refs.form.validate(async (flag) => {
        if (!flag) return
        if (this.$route.query.id) {
          this.ruleForm.subjectID = Number(this.$route.query.id)
        }
        await add(this.ruleForm)
        this.$message.success('操作成功')
        this.handleClose()
        this.$emit('add-directory')
      })
    }
  }
}
</script>

<style scoped lang='scss'>
.container{
  ::v-deep{
    .el-dialog__footer{
      text-align: right;
    }
  }
}

</style>
