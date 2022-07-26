<template>
  <div class='container'>
    <el-dialog
  :title="ruleForm.id ? '编辑' : '添加' "
  :visible="show"
  width="20%"
  @close="closeAdd"
  >
  <el-form ref="form" :rules="rules" :model="ruleForm">
    <el-form-item label="学科名称" prop="subjectName">
    <el-input v-model="ruleForm.subjectName" style="width: 180px;"></el-input>
    </el-form-item>
  </el-form>
  <el-switch
  v-model="ruleForm.isFrontDisplay"
  active-color="#13ce66"
  inactive-color="#ff4949"
  inactive-text="是否显示"
  active-value="1"
  inactive-value="0">
  </el-switch>
  <template #footer>
    <el-button @click="closeAdd">取 消</el-button>
    <el-button type="primary" @click="AddSubject">确 定</el-button>
  </template>
</el-dialog>
  </div>
</template>

<script>
import { add, update } from '@/api/interview/subjects.js'
export default {
  props: {
    show: {
      type: Boolean,
      default: false
    },
    ruleForm: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      rules: {
        subjectName: [{ required: true, message: '学科名称不能为空', trigger: ['blur', 'change'] }]
      }
    }
  },
  methods: {
    closeAdd () {
      this.$emit('update:show', false)
      this.$refs.form.resetFields()
    },
    async AddSubject () {
      this.$refs.form.validate(async (flag) => {
        if (!flag) return
        if (this.ruleForm.id) {
          await update(this.ruleForm)
          this.$message.success('编辑成功')
          this.closeAdd()
          this.$emit('add')
        } else {
          await add(this.ruleForm)
          this.$message.success('添加成功')
          this.closeAdd()
          this.$emit('add')
        }
      })
    }
  }

}
</script>

<style scoped lang='scss'>
::v-deep {
  .el-dialog__footer {
    text-align: right;
  }
}
</style>
