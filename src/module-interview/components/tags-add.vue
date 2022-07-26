<template>
  <div class='container'>
    <el-dialog :title="tabAdd.id ? '编辑' : '添加'" :visible="showTagsAdd" width="400px" @close="closeDialog">
      <el-form :model="form"  label-width="80px" :rules="rules" ref="formRules">
        <el-form-item label="所属学科"  v-if="!$route.query.id" prop="subjectID">
          <el-select v-model="form.subjectID" placeholder="请选择" style="width:100%">
              <el-option v-for="(item) in subjectList" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录名称" prop="tagName">
          <el-input v-model="form.tagName"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeDialog">取 消</el-button>
        <el-button type="primary" @click="clickSubmit">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { add, update, detail } from '@/api/interview/tags'
import { simple } from '@/api/interview/subjects'

export default {
  props: {
    showTagsAdd: {
      type: Boolean,
      default: false
    },
    tabAdd: {
      type: Object,
      required: true
    }
  },
  watch: {
    showTagsAdd (newValue, oldValue) {
      if (newValue === true) {
        this.getSubjectList()
      }
    }
  },

  data () {
    return {
      form: {
        tagName: null,
        subjectID: null
      },
      subjectList: [],
      rules: {
        subjectID: [
          { required: true, message: '请选择所属学科', trigger: ['change', 'blur'] }
        ],
        tagName: [
          { required: true, message: '请输入目录名称', trigger: ['change', 'blur'] }
        ]
      }
    }
  },
  methods: {
    async getSubjectList () {
      if (this.tabAdd.id) {
        const res = await simple()
        this.subjectList = res.data
        const { data: { tagName, subjectID } } = await detail({ id: this.tabAdd.id })
        this.form = { tagName, subjectID }
      } else {
        const res = await simple()
        this.form.subjectID = +this.$route.query.id || null
        this.subjectList = res.data
      }
    },
    closeDialog () {
      this.$emit('update:showTagsAdd', false)
      this.$refs.formRules.resetFields()
    },
    clickSubmit () {
      this.$refs.formRules.validate(async (valid) => {
        if (!valid) return
        if (!this.tabAdd.id) {
          await add(this.form)
        } else {
          await update({ ...this.form, id: this.tabAdd.id })
        }
        this.closeDialog()
        this.$message.success('操作成功')
        this.$emit('submitTabs')
      })
    }
  }
}
</script>

<style scoped lang='scss'>
::v-deep .el-dialog__footer {
  text-align: right;
}
</style>
