<template>
  <div class="ele-body">
    <el-card shadow="never">
      <!-- 搜索表单 -->
      <el-form
        :model="where"
        label-width="77px"
        class="ele-form-search"
        @keyup.enter.native="reload"
        @submit.native.prevent>
        <el-row :gutter="15">
              
          <el-col :lg="6" :md="12">
            <el-form-item label="测试名称:">
              <el-input
                clearable
                v-model="where.name"
                placeholder="请输入测试名称"/>
            </el-form-item>
          </el-col>
                                
          <el-col :lg="6" :md="12">
            <el-form-item label="性别:">
              <el-select
                clearable
                v-model="where.gender"
                placeholder="请选择性别"
                class="ele-fluid">
                              <el-option label="男" value="1"/>
                              <el-option label="女" value="2"/>
                              <el-option label="保密" value="3"/>
                            </el-select>
            </el-form-item>
          </el-col>
                                          
                                  <el-col :lg="6" :md="12">
            <div class="ele-form-actions">
              <el-button
                type="primary"
                icon="el-icon-search"
                class="ele-btn-icon"
                @click="reload">查询
              </el-button>
              <el-button @click="reset">重置</el-button>
            </div>
          </el-col>
        </el-row>
      </el-form>
      <!-- 数据表格 -->
      <ele-pro-table
        ref="table"
        :where="where"
        :datasource="url"
        :columns="columns"
        :selection.sync="selection"
        height="calc(100vh - 315px)">
        <!-- 表头工具栏 -->
        <template slot="toolbar">
          <el-button
            size="small"
            type="primary"
            icon="el-icon-plus"
            class="ele-btn-icon"
            @click="openEdit(null)"
            v-if="permission.includes('sys:example2:add')">添加
          </el-button>
          <el-button
            size="small"
            type="danger"
            icon="el-icon-delete"
            class="ele-btn-icon"
            @click="removeBatch"
            v-if="permission.includes('sys:example2:dall')">删除
          </el-button>
        </template>
        <!-- 操作列 -->
        <template slot="action" slot-scope="{row}">
          <el-link
            type="primary"
            :underline="false"
            icon="el-icon-edit"
            @click="openEdit(row)"
            v-if="permission.includes('sys:example2:edit')">修改
          </el-link>
          <el-popconfirm
            class="ele-action"
            title="确定要删除此演示案例二吗？"
            @confirm="remove(row)">
            <el-link
              type="danger"
              slot="reference"
              :underline="false"
              icon="el-icon-delete"
              v-if="permission.includes('sys:example2:delete')">删除
            </el-link>
          </el-popconfirm>
        </template>
                            <!-- 头像列 -->
          <template slot="avatar" slot-scope="{row}">
            <el-avatar shape="square" :size="25" :src="row.avatar"/>
          </template>
                                        
                                        
          <!-- 性别列 -->
          <template slot="gender" slot-scope="{row}">
                      <el-tag v-if="row.gender === 1" type="" size="small">男</el-tag>
                      <el-tag v-if="row.gender === 2" type="success" size="small">女</el-tag>
                      <el-tag v-if="row.gender === 3" type="warning" size="small">保密</el-tag>
                    </template>
                                                
          <!-- 状态列 -->
          <template slot="status" slot-scope="{row}">
            <el-switch
              v-model="row.status"
              @change="status(row)"
              :active-value="1"
              :inactive-value="2"/>
          </template>
                                                
                                        
                        </ele-pro-table>
    </el-card>
    <!-- 编辑弹窗 -->
    <example2-edit
      :data="current"
      :visible.sync="showEdit"
      @done="reload"/>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Example2Edit from './example2-edit';

export default {
  name: 'ExamExample2',
  components: {Example2Edit},
  computed: {
    ...mapGetters(["permission"]),
  },
  data() {
    return {
      // 表格数据接口
      url: '/example2/index',
      // 表格列配置
      columns: [
        {
          columnKey: 'selection',
          type: 'selection',
          width: 45,
          align: 'center',
          fixed: "left"
        },
        {
          prop: 'id',
          label: 'ID',
          width: 60,
          align: 'center',
          showOverflowTooltip: true,
          fixed: "left"
        },
              {
          columnKey: 'avatar',
          label: '头像',
          align: 'center',
          showOverflowTooltip: true,
          minWidth: 100,
          slot: 'avatar'
        },
                    {
          prop: 'name',
          label: '测试名称',
          showOverflowTooltip: true,
          minWidth: 200,
          align: 'center',
        },
                          {
          prop: 'gender',
          label: '性别',
          align: 'center',
          showOverflowTooltip: true,
          minWidth: 100,
          slot: 'gender',
        },
                                {
          prop: 'status',
          label: '状态',
          align: 'center',
          width: 100,
          resizable: false,
          slot: 'status',
        },
                          {
          prop: 'sort',
          label: '显示顺序',
          showOverflowTooltip: true,
          minWidth: 200,
          align: 'center',
        },
                    {
          prop: 'note',
          label: '备注',
          showOverflowTooltip: true,
          minWidth: 200,
          align: 'center',
        },
              {
          prop: 'create_time',
          label: '创建时间',
          sortable: 'custom',
          showOverflowTooltip: true,
          minWidth: 160,
          align: 'center',
          formatter: (row, column, cellValue) => {
            return this.$util.toDateString(cellValue);
          }
        },
        {
          prop: 'update_time',
          label: '更新时间',
          sortable: 'custom',
          showOverflowTooltip: true,
          minWidth: 160,
          align: 'center',
          formatter: (row, column, cellValue) => {
            return this.$util.toDateString(cellValue);
          }
        },
        {
          columnKey: 'action',
          label: '操作',
          width: 150,
          align: 'center',
          resizable: false,
          slot: 'action',
          fixed: "right"
        }
      ],
      // 表格搜索条件
      where: {},
      // 表格选中数据
      selection: [],
      // 当前编辑数据
      current: null,
      // 是否显示编辑弹窗
      showEdit: false,
    };
  },
  methods: {
    /* 刷新表格 */
    reload() {
      this.$refs.table.reload({where: this.where});
    },
    /* 重置搜索 */
    reset() {
      this.where = {};
      this.reload();
    },
    /* 显示编辑 */
    openEdit(row) {
      this.current = row;
      this.showEdit = true;
    },
    /* 删除 */
    remove(row) {
      const loading = this.$loading({lock: true});
      this.$http.post('/example2/delete', {id: row.id}).then(res => {
        loading.close();
        if (res.data.code === 0) {
          this.$message.success(res.data.msg);
          this.reload();
        } else {
          this.$message.error(res.data.msg);
        }
      }).catch(e => {
        loading.close();
        this.$message.error(e.message);
      });
    },
    /* 批量删除 */
    removeBatch() {
      if (!this.selection.length) {
        this.$message.error('请至少选择一条数据');
        return;
      }
      this.$confirm('确定要删除选中的演示案例二吗?', '提示', {
        type: 'warning'
      }).then(() => {
        const loading = this.$loading({lock: true});
        this.$http.post('/example2/delete', {id: this.selection.map(d => d.id)}).then(res => {
          loading.close();
          if (res.data.code === 0) {
            this.$message.success(res.data.msg);
            this.reload();
          } else {
            this.$message.error(res.data.msg);
          }
        }).catch(e => {
          loading.close();
          this.$message.error(e.message);
        });
      }).catch(() => {
      });
    },
                        
    /* 更改状态 */
    status(row) {
      const loading = this.$loading({lock: true});
      let params = Object.assign({
        "id": row.id,
        "status": row.status      })
      this.$http.post('/example2/status', params).then(res => {
        loading.close();
        if (res.data.code === 0) {
          this.$message.success(res.data.msg);
        } else {
          row.status = !row.status ? 1 : 2;
          this.$message.error(res.data.msg);
        }
      }).catch(e => {
        loading.close();
        this.$message.error(e.message);
      });
    },
                    }
}
</script>

<style scoped>
</style>
