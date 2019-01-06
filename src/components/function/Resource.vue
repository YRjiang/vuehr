<template>
    <div>
      <el-container style="height: 500px; border: 1px solid #eee">
        <el-header style="padding: 0; display:flex;justify-content:space-between;align-items: center">
          <el-button @click="dialogVisible = true" icon="el-icon-plus" type="primary" size="mini">添加账套</el-button>
          <el-button size="mini" type="success" @click="loadSalaryCfg" icon="el-icon-refresh"></el-button>
        </el-header>
      </el-container>

    </div>
</template>

<script>
    export default {
      data() {
        return{
          key:'',
          resource:{},
          rule:{}
        };
      },
      mounted: function() {
        this.data();
        this.toString();
      },
      methods: {
        addResource(){

        },
        getDate(){

        },
        data(){

        },
        getAllMenu(){

        }
      }
    }
</script>

<style scoped>

</style>
