<template>
  <el-dialog :title="textMap[dialogStatus]"
             :visible.sync="dialogFormVisible"
             width="50%"
             @open="handleOpen">
    <el-form ref="dataForm"
             :rules="rules"
             :model="formData"
             label-position="top"
             label-width="140px">
      <el-form-item label="标题"
                    prop="title">
        <el-input v-model="formData.title"
                  placeholder="请设置" />
      </el-form-item>
      <el-form-item label="banner图片"
                    prop="bannerUrl">
        <div style="margin-bottom: 20px;">
          <Upload v-model="formData.bannerUrl" />
        </div>
      </el-form-item>
      <el-form-item label="分享小图标"
                    prop="shareIcon">
        <div style="margin-bottom: 20px;">
          <Upload v-model="formData.shareIcon" />
        </div>
      </el-form-item>
      <el-form-item label="点击链接"
                    prop="bannerClickUrl">
        <el-input v-model="formData.bannerClickUrl"
                  placeholder="请设置" />
      </el-form-item>
      <el-form-item label="分享地址"
                    prop="shareUrl">
        <el-input v-model="formData.shareUrl"
                  placeholder="请设置" />
      </el-form-item>
      <el-form-item label="分享标题"
                    prop="shareTitle">
        <el-input v-model="formData.shareTitle"
                  placeholder="请设置" />
      </el-form-item>
      <el-form-item label="分享副标题"
                    prop="shareSubtitle">
        <el-input v-model="formData.shareSubtitle"
                  placeholder="请设置" />
      </el-form-item>
      <el-form-item label="类型"
                    prop="bannerType">
        <el-select v-model="formData.bannerType"
                   class="filter-item"
                   placeholder="请选择">
          <el-option v-for="(item,index) in bannerTypeOptions"
                     :key="index"
                     :label="item.value"
                     :value="item.key" />
        </el-select>
      </el-form-item>
      <el-form-item label="分享渠道"
                    prop="shareChannel">
        <el-select v-model="formData.shareChannel"
                   class="filter-item"
                   placeholder="请选择"
                   multiple
                   width="30%">
          <el-option v-for="(item,index) in shareChannelOptions"
                     :key="index"
                     :label="item.value"
                     :value="item.key" />
        </el-select>
      </el-form-item>
    </el-form>
    <div slot="footer"
         class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取消</el-button>
      <el-button type="primary"
                 @click="dialogStatus==='create'?createData():updateData()">确认
      </el-button>
    </div>
  </el-dialog>

</template>

<script>
import { create, modify } from '@/api/activity/banner'
import Upload from '@/components/Upload/singleImage3'
import waves from '@/directive/waves' // Waves directive
import permission from '@/directive/permission'

export default {
  directives: { waves, permission },
  components: { Upload },
  props: {
    formData: {
      type: Object,
      default: () => ({})
    },
    bannerTypeOptions: {
      type: Array,
      default: () => ([])
    },
    shareChannelOptions: {
      type: Array,
      default: () => ([])
    }
  },
  data () {
    return {
      textMap: {
        update: '编辑Banner',
        create: '新建Banner'
      },
      dialogStatus: undefined,
      dialogFormVisible: false,
      rules: {
        title: [{ required: true, message: '活动标题不能为空', trigger: 'blur' }],
        bannerUrl: [{ required: true, message: 'banner图片地址不能为空', trigger: 'blur' }],
        shareUrl: [{ required: true, message: '分享地址不能为空', trigger: 'blur' }],
        shareTitle: [{ required: true, message: '分享标题不能为空', trigger: 'blur' }],
        shareSubtitle: [{ required: true, message: '分享副标题不能为空', trigger: 'blur' }],
        shareIcon: [{ required: true, message: '分享图标不能为空', trigger: 'blur' }],
        bannerType: [{ required: true, message: 'Banner 类型不能为空', trigger: 'blur' }],
        shareChannel: [{ required: true, message: '分享渠道不能为空', trigger: 'blur' }]
      }
    }
  },
  methods: {
    handleOpen () {
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    createData () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.formData.createUser = this.$store.state.user.username
          this.formData.updateUser = this.$store.state.user.username
          create(this.formData).then(() => {
            this.dialogFormVisible = false
            this.$notify({
              title: '成功',
              message: '创建成功',
              type: 'success',
              duration: 2000
            })
            this.$parent.getBannerList()
          })
        }
      })
    },
    updateData () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          const tempData = Object.assign({}, this.formData)
          tempData.updateUser = this.$store.state.user.username
          modify(tempData).then(() => {
            this.dialogFormVisible = false
            this.$notify({
              title: '成功',
              message: '更新成功',
              type: 'success',
              duration: 2000
            })
            this.$parent.getBannerList()
          })
        }
      })
    }
  }
}
</script>
