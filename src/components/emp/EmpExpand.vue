<template>
  <div>
    <el-container>

      <el-header style="padding: 0px; display:flex; justify-content:space-between; align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过员工名搜索员工,记得回车哦..."
            clearable
            @change="keywordsChange"
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            :disabled="advanceSearchViewVisible"
            @keyup.enter.native="searchEmp"
            prefix-icon="el-icon-search"
            v-model="keywords">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp">搜索
          </el-button>

          <el-button type="primary" size="mini" icon="el-icon-plus"
                     @click="showAddMenuView">
            添加菜单
          </el-button>

          <el-button type="primary" size="mini" icon="el-icon-plus"
                     @click="businessService">
            业务测试按钮
          </el-button>

        </div>

        <div>
          <el-button size="mini" type="success" @click="loadMenu" icon="el-icon-refresh"></el-button>
        </div>
      </el-header>

      <el-main style="padding-left: 0px; padding-top: 0px">
        <div>
          <el-table
            :data="menuData"
            stripe
            v-loading="tableLoading"
            size="mini"
            @selection-change="handleSelectionChange"
            style="width: 100%">

            <el-table-column
              type="selection"
              align="center"
              width="50">
            </el-table-column>
            <el-table-column
              width="120"
              align="center"
              prop="name"
              label="名称">
            </el-table-column>
            <el-table-column
              width="180"
              align="center"
              prop="url"
              label="URL">
            </el-table-column>
            <el-table-column
              width="120"
              align="center"
              prop="path"
              label="路径">
            </el-table-column>
            <el-table-column
              width="90"
              align="center"
              prop="component"
              label="组件">
            </el-table-column>
            <el-table-column
              width="100"
              align="center"
              prop="iconCls"
              label="图标">
            </el-table-column>
            <el-table-column
              width="80"
              align="center"
              prop="parentId"
              label="上一级ID">
            </el-table-column>
            <el-table-column
              width="80"
              align="center"
              prop="enabled"
              label="使能开关">
            </el-table-column>
            <el-table-column label="父子级联" align="center">
              <el-table-column
                width="50"
                align="center"
                prop="id"
                label="ID">
              </el-table-column>
              <el-table-column
                width="50"
                align="center"
                prop="parentId"
                label="父ID">
              </el-table-column>
            </el-table-column>

            <el-table-column label="操作" align="center">
              <el-table-column label="编辑" align="center">
                <template slot-scope="scope">
                  <el-button
                    size="mini"
                    @click="handleEdit(scope.$index, scope.row)">编辑
                  </el-button>
                </template>
              </el-table-column>
              <el-table-column label="删除" align="center">
                <template slot-scope="scope">
                  <el-button
                    size="mini"
                    type="danger"
                    @click="deleteMenu(scope.row)">删除
                  </el-button>
                </template>
              </el-table-column>
            </el-table-column>

          </el-table>

          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="danger" size="mini" v-if="menuData.length>0" :disabled="multipleSelection.length==0"
                       @click="deleteManyMenu">批量删除
            </el-button>
            <el-pagination
              background
              :page-size="10"
              :current-page="currentPage"
              @current-change="currentChange"
              layout="prev, pager, next"
              :total="totalCount">
            </el-pagination>
          </div>

        </div>
      </el-main>
    </el-container>

    <el-form :model="menu" :rules="rules" ref="addMenuForm" style="margin: 0px;padding: 0px;">
      <div style="text-align: left">
        <el-dialog
          :title="dialogTitle"
          style="padding: 0px;"
          :close-on-click-modal="false"
          :visible.sync="dialogVisible"
          width="77%">

          <el-row>
            <el-col :span="6">
              <div>
                <el-form-item label="姓名:" prop="name">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.name" size="mini" style="width: 150px"
                            placeholder="请输入员工姓名"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="5">
              <div>
                <el-form-item label="URL:" prop="url">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.url" size="mini" style="width: 150px"
                            placeholder="请输入url"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="6">
              <div>
                <el-form-item label="Path:" prop="path">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.path" size="mini" style="width: 150px"
                            placeholder="请输入path"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="7">
              <div>
                <el-form-item label="组件:" prop="component">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.component" size="mini" style="width: 150px"
                            placeholder="请输入组件"></el-input>
                </el-form-item>
              </div>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="6">
              <div>
                <el-form-item label="父ID:" prop="parentId">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.parentId" size="mini" style="width: 150px"
                            placeholder="请输入员工父ID"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="5">
              <div>
                <el-form-item label="ID:" prop="id">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.id" size="mini" style="width: 150px"
                            placeholder="请输入ID"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="6">
              <div>
                <el-form-item label="图标:" prop="iconCls">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.iconCls" size="mini" style="width: 150px"
                            placeholder="请输入图标"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="7">
              <div>
                <el-form-item label="使能开关:" prop="enabled">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.enabled" size="mini" style="width: 150px"
                            placeholder="请选择使能开关"></el-input>
                </el-form-item>
              </div>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="6">
              <div>
                <el-form-item label="keepAlive:" prop="keepAlive">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.keepAlive" size="mini" style="width: 150px"
                            placeholder="请输入 keepAlive"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="5">
              <div>
                <el-form-item label="requireAuth:" prop="requireAuth">
                  <el-input prefix-icon="el-icon-edit" v-model="menu.requireAuth" size="mini" style="width: 150px"
                            placeholder="请输入 requireAuth"></el-input>
                </el-form-item>
              </div>
            </el-col>
          </el-row>

          <span slot="footer" class="dialog-footer">
            <el-button size="mini" @click="cancelEidt">取 消</el-button>
            <el-button size="mini" type="primary" @click="addMenu('addMenuForm')">确 定</el-button>
          </span>

        </el-dialog>
      </div>
    </el-form>

  </div>
</template>

<script>
    export default {
        //name: "EmpExpand"
      data(){
        return{
          menuData: [],
          multipleSelection: [],
          dialogTitle: '',
          dialogVisible: false,
          totalCount: -1,
          currentPage: 1,
          menu:{
            name: '添加菜单',
            id: 709,
            url: '/function/eidt/**',
            path: '/function/edit',
            component: 'Edit',
            iconCls: 'fa-fa',
            keepAlive: '',
            requireAuth: 1,
            parentId: 7,
            enabled: 1
          },
          rules: {
            name: [{required: true, message: '必填:姓名', trigger: 'blur'}],
            gender: [{required: true, message: '必填:性别', trigger: 'blur'}],
            birthday: [{required: true, message: '必填:出生日期', trigger: 'blur'}],
            idCard: [{
              required: true,
              message: '必填:身份证号码',
              trigger: 'blur'
            }, {
              pattern: /(^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$)|(^[1-9]\d{5}\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{2}$)/,
              message: '身份证号码格式不正确',
              trigger: 'blur'
            }],
            wedlock: [{required: true, message: '必填:婚姻状况', trigger: 'blur'}],
            nationId: [{required: true, message: '必填:民族', trigger: 'change'}],
            nativePlace: [{required: true, message: '必填:籍贯', trigger: 'blur'}],
            politicId: [{required: true, message: '必填:政治面貌', trigger: 'blur'}],
            email: [{required: true, message: '必填:电子邮箱', trigger: 'blur'}, {
              type: 'email',
              message: '邮箱格式不正确',
              trigger: 'blur'
            }],
            phone: [{required: true, message: '必填:电话号码', trigger: 'blur'}],
            address: [{required: true, message: '必填:联系地址', trigger: 'blur'}],
            departmentId: [{required: true, message: '必填:部门', trigger: 'change'}],
            jobLevelId: [{required: true, message: '必填:职称', trigger: 'change'}],
            posId: [{required: true, message: '必填:职位', trigger: 'change'}],
            engageForm: [{required: true, message: '必填:聘用形式', trigger: 'blur'}],
            tiptopDegree: [{required: true, message: '必填:最高学历', trigger: 'change'}],
            specialty: [{required: true, message: '必填:专业', trigger: 'blur'}],
            workID: [{required: true, message: '必填:工号', trigger: 'blur'}],
            school: [{required: true, message: '必填:毕业院校', trigger: 'blur'}],
            beginDate: [{required: true, message: '必填:入职日期', trigger: 'blur'}],
            conversionTime: [{required: true, message: '必填:转正日期', trigger: 'blur'}],
            beginContract: [{required: true, message: '必填:合同起始日期', trigger: 'blur'}],
            endContract: [{required: true, message: '必填:合同终止日期', trigger: 'blur'}],
            workAge: [{required: true, message: '必填:工龄', trigger: 'blur'}]
          }
        };
      },
      mounted: function() {
        this.loadMenu();
      },
      methods:{
        loadMenu(){
          this.tableLoading = true;
          var _this = this;
          this.getRequest("/function/resource/loadMenu").then(resp=> {
            _this.tableLoading = false;
            if (resp && resp.status == 200) {
              _this.menuData = resp.data;
            }
          })
        },
        showAddMenuView(){
          this.dialogTitle = "添加菜单界面";
          this.dialogVisible = true;
          var _this = this;
          this.getRequest("/employee/basic/maxWorkID").then(resp=> {
            if (resp && resp.status == 200) {
              _this.emp.workID = resp.data;
            }
          })
        },
        addMenu(formName){
          var _this = this;
          this.$refs[formName].validate((valid) => {
            if (valid) {
              if (this.menu.id) {
                //更新
                this.tableLoading = true;
                this.postRequest("/function/resource/add", this.menu).then(resp=> {
                  _this.tableLoading = false;
                  if (resp && resp.status == 200) {
                    var data = resp.data;
                    _this.dialogVisible = false;
                    _this.emptyMenuData();
                    _this.loadMenu();
                  }
                })
              }
            } else {
              return false;
            }
          });
        },
        deleteManyMenu(){
          this.$confirm('此操作将删除[' + this.multipleSelection.length + ']条数据, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            var ids = '';
            for (var i = 0; i < this.multipleSelection.length; i++) {
              ids += this.multipleSelection[i].id + ",";
            }
            this.doDelete(ids);
          }).catch(() => {
          });
        },
        deleteMenu(row){
          this.$confirm('此操作将永久删除[' + row.name + '], 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.doDelete(row.id);
          }).catch(() => {
          });
        },
        doDelete(ids){
          this.tableLoading = true;
          var _this = this;
          this.deleteRequest("/function/resource/delete/" + ids).then(resp=> {
            _this.tableLoading = false;
            if (resp && resp.status == 200) {
              _this.loadMenu();
            }
          })
        },
        cancelEidt(){
          this.dialogVisible = false;
          this.emptyMenuData();
        },
        emptyMenuData(){
          this.menu = {
            name: '',
            id: '',
            url: '',
            path: '',
            component: '',
            iconCls: '',
            keepAlive: '',
            requireAuth: '',
            parentId: '',
            enabled: ''
          }
        },
        businessService(){
          this.postRequest("/templates/email/attachmentsMail").then(resp=> {
            if (resp && resp.status == 200) {
              var data = resp.data;
            }
          })
        }
      }
    }
</script>

<style scoped>

</style>
