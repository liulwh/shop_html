<template>
    <div>
       <h1>权限管理</h1>
      <el-button type="primary" plain v-on:click="addTreeFlag=true">新增按钮</el-button>

      <el-tree
        :data="data"
        :props="defaultProps"
        accordion
        @node-click="handleNodeClick"
      >
      <span class="custom-tree-node" slot-scope="{ node, data }">
  <span>{{ node.label }}</span>
  <span>
    <el-button
      type="text"
      size="mini"
      v-on:click="updateFormFlag=true">
      修改
    </el-button>

  </span>
</span>

      </el-tree>

      <!-- 新增模板-->

      <el-dialog title="录入tree信息" :visible.sync="addTreeFlag">
        <el-form  :model="form"    label-width="80px">

          <el-form-item label="上级权限名称">
            <el-input v-model="form.label" disabled=""></el-input>
          </el-form-item>

          <el-form-item label="权限名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>

          <el-form-item label="路径">
            <el-input v-model="form.url"></el-input>
          </el-form-item>

          <el-form-item label="类型" prop="type">
            <el-radio v-model="form.type" :label="0">目录</el-radio>
            <el-radio v-model="form.type" :label="1">菜单</el-radio>
          </el-form-item>



          <el-form-item label="pid">
            <el-input v-model="form.pid"></el-input>
          </el-form-item>


          <el-form-item>
            <el-button @click="addTreeFlag = false">取 消</el-button>
            <el-button @click="addTree">新 增</el-button>

          </el-form-item>
        </el-form>

      </el-dialog>


      <!--修改模板-->
      <el-dialog title="修改权限信息" :visible.sync="updateFormFlag">

        <el-form :model="updateTypeForm" ref="addTypeForm"  label-width="80px">
          <el-form-item label="pid" prop="pid">
            <el-input v-model="updateTypeForm.pid"  ></el-input>
          </el-form-item>


          <el-form-item label="名称" prop="name">
            <el-input v-model="updateTypeForm.name"  ></el-input>
          </el-form-item>

          <el-form-item label="路径" prop="url">
            <el-input v-model="updateTypeForm.url" ></el-input>
          </el-form-item>

          <el-form-item label="类型" prop="type">
            <el-radio v-model="updateTypeForm.type" :label="0" >目录</el-radio>
            <el-radio v-model="updateTypeForm.type" :label="1" >菜单</el-radio>
          </el-form-item>

        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="updateFormFlag = false">取 消</el-button>
          <el-button type="primary" @click="updateForm">确 定</el-button>
        </div>

      </el-dialog>

    </div>
</template>

<script>
    export default {
        name: "Permission"
      ,
      data(){
        return{
          data:[],//tree 的数据
          ajaxData:[],// 远程请求的数据
          jsonStr:"", //递归拼接处理
          defaultProps:{},
          addTreeFlag:false,
          updateFormFlag:false,
          updateTypeForm:{
            pid:"",
            name:"",
            url:"",
            type:""
          },
          form:{
            pid:"",
            name:""
          }
        }
      },methods:{
        chandleData:function(){ //ajaxData  处理成 data   //转换数据

          //找到顶层节点的数据
          for (let i = 0; i <this.ajaxData.length ; i++) {
            if(this.ajaxData[i].pid==0){
              this.diguiNode(this.ajaxData[i]);
              break;
            }
          }
          console.log(this.jsonStr);
          //将字符串 转为json对象
          this.data.push(JSON.parse(this.jsonStr));

        },  //  id  name  pid        id  name children []
        diguiNode:function (node) {
          // 判断是否为父节点
          var bf=this.isParent(node);
          if(bf==true){
            //{"id":1,"label":"分类",}
            //{"id":1,"label":"分类","children":[]} ,"label":"'+node.name+'",
            this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'","url":"'+node.url+'","type":"'+node.type+'","children":[';
            //拼接子节点
            //查找此节点的子节点
            let count=0
            for (let i = 0; i <this.ajaxData.length ; i++) {
              //判断是否为当前节点的子节点
              if(node.id==this.ajaxData[i].pid){
                count++;
                this.diguiNode(this.ajaxData[i]);
                this.jsonStr+=",";
              }
            }
            //处理多余的,号
            if(count!=0){
              this.jsonStr=this.jsonStr.substr(0,this.jsonStr.length-1);
            }



            //拼完整
            this.jsonStr+=']}';
          }else{
            this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'","url":"'+node.url+'","type":"'+node.type+'"}';
          }

        }
        ,isParent:function(node){ // 判断是否为父节点  pid 有没有指向当前id

          for (let i = 0; i <this.ajaxData.length ; i++) {
            if(node.id==this.ajaxData[i].pid){
              return true;
            }
          }
          return false;
        },handleNodeClick(data) {

          var  athis=this;
          var bthis=this;
          console.log(data);
          athis.form=data;
          athis.form.pid=data.id
         // console.log(athis.form);
          bthis.updateTypeForm.name=data.label;
          bthis.updateTypeForm.pid=data.id;
          bthis.updateTypeForm=data;
          console.log( bthis.updateTypeForm);

        },addTree:function(){

          var athis=this;
          this.$ajax.post("http://localhost:8080/api/permission/add",this.$qs.stringify(athis.form)).then(function () {
            athis.addTreeFlag=false;


          }).catch(function () {

            alert("新增异常");
          })

        },updateForm:function () {
          var athis=this;
          this.$ajax.post("http://localhost:8080/api/permission/update",this.$qs.stringify(athis.updateTypeForm)).then(function () {
            athis.updateFormFlag=false;


          }).catch(function () {

            alert("修改异常");
          })
        }

      },created:function(){
        //远程请求数据
        this.$ajax.get("http://localhost:8080/api/permission/getData").then(res=>{
          this.ajaxData=res.data.data;  // 把请求的数据  赋给全局
          this.chandleData();

          console.log(this.ajaxData)
        }).catch(err=>console.log(err));
      }
    }
</script>

<style scoped>

</style>
