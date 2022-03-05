<!-- 演示案例一编辑弹窗 -->
<template>
  <el-dialog
    :title="isUpdate?'修改演示案例一':'添加演示案例一'"
    :visible="visible"
    width="550px"
    :destroy-on-close="true"
    :lock-scroll="false"
    @update:visible="updateVisible">
    <el-form
      ref="form"
      :model="form"
      :rules="rules"
      label-width="100px">
      
      <el-form-item label="头像:">
        <uploadImage :limit="1" v-model="form.avatar"></uploadImage>
      </el-form-item>
                        
      <el-form-item
        label="测试名称:"
        prop="name">
        <el-input
          :maxlength="20"
          v-model="form.name"
          placeholder="请输入测试名称"
          clearable/>
      </el-form-item>
                      
      <el-form-item label="性别:" prop="gender">
        <el-select
          clearable
          class="ele-block"
          v-model="form.gender"
          placeholder="请选择性别">
                  <el-option label="男" :value="1"/>
                  <el-option label="女" :value="2"/>
                  <el-option label="保密" :value="3"/>
                </el-select>
      </el-form-item>
                      
      <el-form-item label="类型:" prop="type">
        <el-select
          clearable
          class="ele-block"
          v-model="form.type"
          placeholder="请选择类型">
                  <el-option label="董事长" :value="1"/>
                  <el-option label="总经理" :value="2"/>
                  <el-option label="部门总监" :value="3"/>
                  <el-option label="部门经理" :value="4"/>
                  <el-option label="部门主管" :value="5"/>
                  <el-option label="普工" :value="6"/>
                </el-select>
      </el-form-item>
                      
      <el-form-item label="状态:">
        <el-radio-group
          v-model="form.status">
                  <el-radio :label="1">正常</el-radio>
                  <el-radio :label="2">停用</el-radio>
                </el-radio-group>
      </el-form-item>
                      
      <el-form-item label="是否VIP:">
        <el-radio-group
          v-model="form.is_vip">
                  <el-radio :label="1">是</el-radio>
                  <el-radio :label="2">否</el-radio>
                </el-radio-group>
      </el-form-item>
                              
      <el-form-item label="显示顺序:" prop="sort">
        <el-input-number
          :min="0"
          v-model="form.sort"
          placeholder="请输入显示顺序"
          controls-position="right"
          class="ele-fluid ele-text-left"/>
      </el-form-item>
                
      <el-form-item label="备注:">
        <el-input
          :rows="3"
          clearable
          type="textarea"
          :maxlength="200"
          v-model="form.note"
          placeholder="请输入备注"/>
      </el-form-item>
          </el-form>
    <div slot="footer">
      <el-button @click="updateVisible(false)">取消</el-button>
      <el-button
        type="primary"
        @click="save"
        :loading="loading">保存
      </el-button>
    </div>
  </el-dialog>
</template>

<script>
import uploadImage from '@/components/uploadImage'

export default {
  name: 'ExampleEdit',
  props: {
    // 弹窗是否打开
    visible: Boolean,
    // 修改回显的数据
    data: Object
  },
  components: {uploadImage},
  data() {
    return {
      // 表单数据
      form: Object.assign({status:1}, this.data),
      // 表单验证规则
      rules: {
              avatar: [
          {required: true, message: '请输入头像', trigger: 'blur'}
        ],
                  name: [
          {required: true, message: '请输入测试名称', trigger: 'blur'}
        ],
                  
        gender: [
          {required: true, message: '请选择性别', trigger: 'blur'}
        ],
                          
        type: [
          {required: true, message: '请选择类型', trigger: 'blur'}
        ],
                          
        status: [
          {required: true, message: '请选择状态', trigger: 'blur'}
        ],
                          
        is_vip: [
          {required: true, message: '请选择是否VIP', trigger: 'blur'}
        ],
                          sort: [
          {required: true, message: '请输入显示顺序', trigger: 'blur'}
        ],
                  note: [
          {required: true, message: '请输入备注', trigger: 'blur'}
        ],
            },
      // 提交状态
      loading: false,
      // 是否是修改
      isUpdate: false
    };
  },
  watch: {
    data() {
      if (this.data) {
        this.form = Object.assign({}, this.data);
        this.isUpdate = true;
      } else {
        this.form = {};
        this.isUpdate = false;
      }
    }
  },
  methods: {
    /* 保存编辑 */
    save() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
          this.loading = true;
          this.$http.post('/example/edit', this.form).then(res => {
            this.loading = false;
            if (res.data.code === 0) {
              this.$message.success(res.data.msg);
              if (!this.isUpdate) {
                this.form = {};
              }
              this.updateVisible(false);
              this.$emit('done');
            } else {
              this.$message.error(res.data.msg);
            }
          }).catch(e => {
            this.loading = false;
            this.$message.error(e.message);
          });
        } else {
          return false;
        }
      });
    },
    /* 更新visible */
    updateVisible(value) {
      this.$emit('update:visible', value);
    }
  }
}
</script>

<style scoped>
</style>
