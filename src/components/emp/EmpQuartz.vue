<template>
  <div>
    <el-container>

      <el-header style="padding: 0px; display:flex; justify-content:space-between; align-items: center">
        <div style="display: inline">

          <el-button type="primary" size="mini" icon="el-icon-plus"
                     @click="showAddMenuView">
            添加定时任务
          </el-button>

        </div>

      </el-header>

      <el-main style="padding-left: 0px; padding-top: 0px">
        <div>
          <el-table
            :data="jobData"
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
              width="100"
              align="center"
              prop="jobName"
              label="任务名称">
            </el-table-column>
            <el-table-column
              width="100"
              align="center"
              prop="jobGroup"
              label="任务分组">
            </el-table-column>
            <el-table-column
              width="150"
              align="center"
              prop="description"
              label="任务描述">
            </el-table-column>
            <el-table-column
              width="150"
              align="center"
              prop="jobClassName"
              label="执行类">
            </el-table-column>
            <el-table-column
              width="120"
              align="center"
              prop="cronExpression"
              label="执行时间">
            </el-table-column>
            <el-table-column
              width="120"
              align="center"
              prop="triggerName"
              label="触发器名称">
            </el-table-column>
            <el-table-column
              width="120"
              align="center"
              prop="triggerState"
              label="触发器状态">
            </el-table-column>

            <el-table-column label="操作" align="center">
              <el-table-column label="编辑" align="center">
                <template slot-scope="scope">
                  <el-button
                    size="mini"
                    @click="handleEdit(scope.row)">编辑
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

        </div>
      </el-main>
    </el-container>

    <el-form :model="job" :rules="rules" ref="addJobForm" style="margin: 0px;padding: 0px;">
      <div style="text-align: left">
        <el-dialog
          :title="dialogTitle"
          style="padding: 0px;"
          :close-on-click-modal="false"
          :visible.sync="dialogVisible"
          width="77%">

          <el-row>
            <el-col :span="7">
              <div>
                <el-form-item label="任务名称:" prop="name">
                  <el-input prefix-icon="el-icon-edit" v-model="job.jobName" size="mini" style="width: 150px"
                            placeholder="请输入任务名称"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="7">
              <div>
                <el-form-item label="任务分组:" prop="url">
                  <el-input prefix-icon="el-icon-edit" v-model="job.jobGroup" size="mini" style="width: 150px"
                            placeholder="请输入任务分组"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="7">
              <div>
                <el-form-item label="任务描述:" prop="path">
                  <el-input prefix-icon="el-icon-edit" v-model="job.description" size="mini" style="width: 150px"
                            placeholder="请输入任务描述"></el-input>
                </el-form-item>
              </div>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="7">
              <div>
                <el-form-item label="执行类  :" prop="component">
                  <el-input prefix-icon="el-icon-edit" v-model="job.jobClassName" size="mini" style="width: 150px"
                            placeholder="请输入执行类"></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="7">
              <div>
                <el-form-item label="执行时间:" prop="parentId">
                  <el-input prefix-icon="el-icon-edit" v-model="job.cronExpression" size="mini" style="width: 150px"
                            placeholder="请输入执行时间"></el-input>
                </el-form-item>
              </div>
            </el-col>
          </el-row>
          <!--<el-row>
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
          </el-row>-->

          <span slot="footer" class="dialog-footer">
            <el-button size="mini" @click="cancelEidt">取 消</el-button>
            <el-button size="mini" type="primary" @click="addJob('addJobForm')">确 定</el-button>
          </span>

        </el-dialog>
      </div>
    </el-form>

  </div>
</template>

<script>
  export default {
    //name: "EmpQuartz"
    data(){
      return{
        jobData: [],
        multipleSelection: [],
        dialogTitle: '',
        dialogVisible: false,
        job: {
          jobName: '',
          jobGroup: '',
          description: '',
          jobClassName: '',
          cronExpression: '',
          triggerName: '',
          triggerState: '',
          oldJobName: '',
          oldJobGroup: ''
        },
        rules: {
          jobName: [{required: true, message: '必填:姓名', trigger: 'blur'}],
          jobGroup: [{required: true, message: '必填:性别', trigger: 'blur'}],
          description: [{required: true, message: '必填:职称', trigger: 'change'}],
          jobClassName: [{required: true, message: '必填:职位', trigger: 'change'}],
          cronExpression: [{required: true, message: '必填:聘用形式', trigger: 'blur'}]
        }
      };
    },
    mounted: function() {
      this.loadJob();
    },
    methods:{
      loadJob(){
        this.tableLoading = true;
        var _this = this;
        this.postRequest("/job/list", this.job).then(resp=> {
          _this.tableLoading = false;
          if (resp && resp.status == 200) {
            _this.jobData = resp.data;
          }
        })
      },
      showAddMenuView(){
        this.dialogTitle = "添加定时任务界面";
        this.dialogVisible = true;
        this.emptyJobData();
        var _this = this;
        this.getRequest("/employee/basic/maxWorkID").then(resp=> {
          if (resp && resp.status == 200) {
            _this.emp.workID = resp.data;
          }
        })
      },
      addJob(formName){
        var _this = this;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            if (this.job.jobName) {
              //更新
              this.tableLoading = true;
              this.postRequest("/job/add", this.job).then(resp=> {
                _this.tableLoading = false;
                if (resp && resp.status == 200) {
                  var data = resp.data;
                  _this.dialogVisible = false;
                  _this.emptyJobData();
                  _this.loadJob();
                }
              })
            }
          } else {
            return false;
          }
        });
      },
      deleteMenu(row){
        this.$confirm('此操作将永久删除[' + row.jobName + '], 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.doDelete(row);
        }).catch(() => {
        });
      },
      doDelete(job){
        this.tableLoading = true;
        var _this = this;
        this.postRequest("/job/remove", job).then(resp=> {
          _this.tableLoading = false;
          if (resp && resp.status == 200) {
            _this.loadJob();
          }
        })
      },
      handleEdit(row){
        console.log(row)
        this.dialogTitle = "编辑任务";
        this.job = row;
        this.job.jobName = row.jobName;
        this.job.jobGroup = row.jobGroup;
        this.job.description = row.description;
        this.job.jobClassName = row.jobClassName;
        this.job.cronExpression = row.cronExpression;
        this.dialogVisible = true;
      },
      cancelEidt(){
        this.dialogVisible = false;
        this.emptyJobData();
      },
      emptyJobData(){
        this.job = {
          jobName: '',
          jobGroup: '',
          description: '',
          jobClassName: '',
          cronExpression: '',
          triggerName: '',
          triggerState: '',
          oldJobName: '',
          oldJobGroup: ''
        }
      }
    }
  }
</script>

<style scoped>

</style>
