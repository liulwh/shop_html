<template>


  <div>
    <el-steps :active="active" finish-status="success">
      <el-step title="填写商品信息"></el-step>
      <el-step title="填写商品属性"></el-step>
    </el-steps>
    <div align="center" v-if="!diyibu">
      <el-form :model="addShangPin" ref="addAttrForm"  label-width="80px">

        <el-form-item label="商品分类">
          <el-select v-model="addShangPin.typeId" placeholder="请选择分类">
            <el-option label="请选择" :value="-1"></el-option>
            <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item style="width:400px" label="商品名称" prop="name">
          <el-input v-model="addShangPin.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item align="center" style="width:400px" label="商品标题" prop="nameCH">
          <el-input v-model="addShangPin.nameCH"></el-input>
        </el-form-item>
        <el-form-item align="center" label="品牌分类" prop="type">
          <el-select v-model="addShangPin.typeId" placeholder="请选择">
            <el-option v-for="item in Pinpais" :key="item.value" :label="item.name" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item style="width:400px"  label="商品介绍" prop="typeId">
          <el-input type="textarea" v-model="addShangPin.nameCH"></el-input>
        </el-form-item>
        <el-form-item align="center" style="width:400px" label="价格" prop="nameCH">
          <el-input v-model="addShangPin.nameCH"></el-input>
        </el-form-item>
        <el-form-item align="center" style="width:400px" label="库存" prop="nameCH">
          <el-input v-model="addShangPin.nameCH"></el-input>
        </el-form-item>
        <el-form-item align="center" style="width:400px" label="排序" prop="nameCH">
          <el-input v-model="addShangPin.nameCH"></el-input>
        </el-form-item>
      </el-form>
      <el-button style="margin-top: 12px;" @click="next">下一步</el-button>
    </div>
    <!--第二步 -->
    <div v-if="diyibu" >
      <el-form align="center" :model="addShangPin" ref="addAttrForm"  label-width="80px">
        <el-form-item align="center" label="品牌分类" prop="type">
          <el-select v-model="addShangPin.typeId" placeholder="请选择">
            <el-option v-for="item in types" :key="item.value" :label="item.name" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item style="width:400px" label="商品规格" prop="name">
          <el-input v-model="addShangPin.name" autocomplete="off" ></el-input>
        </el-form-item>
        <el-button style="margin-top: 12px;" @click="nextShang">上一步</el-button>
        <el-button style="margin-top: 12px;" @click="next">下一步</el-button>
      </el-form>
    </div>
  </div>




</template>

<script>
    export default {
        name: "Shop",
      data(){
          return{
            active: 0,
            addShangPin:{},
            types:[],
            num: 1,
            diyibu:false,
            Pinpais:[],
            ajaxTypeData:[],
            typeName:"",

          }
    },methods:{
      queryTypeData:function(){

        this.$ajax.get("http://localhost:8080/api/type/getData").then(res=>{

          // [{id:1,"name":"",pid:2},{}]
          this.ajaxTypeData=res.data.data;
          //{"id":7,name:"分类/电子产品/手机"},
          //先找到子节点的数据   this.types;
          this.getChildrenType();

          //遍历所有的子节点
          for (let i = 0; i <this.types.length ; i++) {
            this.typeName=""; // 全局变量   临时存 层级名称
            //处理子节点的name属性
            this.chandleName(this.types[i]);
            //   a/b/c/f/d/e
            //给name重新赋值
            this.types[i].name=this.typeName.split("/").reverse().join("/");
          }

        })
      }, //给我一个节点  得到层级name
      chandleName:function(node){
        if(node.pid!=0){ //临界值
          this.typeName+="/"+node.name;
          //上级节点
          for (let i = 0; i <this.ajaxTypeData.length ; i++) {
            if(node.pid==this.ajaxTypeData[i].id){
              this.chandleName(this.ajaxTypeData[i]);
              break;
            }
          }

        }else{
          this.typeName+="/"+node.name;
        }
      },
      //得到types的数据      遍历所有ajaxtypedata
      getChildrenType:function(){
        //遍历所有的节点数据
        for (let i = 0; i <this.ajaxTypeData.length ; i++) {
          let  node=this.ajaxTypeData[i];
          this.isChildrenNode(node);
        }
      },
      isChildrenNode:function(node){
        let rs=true; //标示
        for (let i = 0; i <this.ajaxTypeData.length ; i++) {
          if(node.id==this.ajaxTypeData[i].pid){
            rs=false;
            break;
          }
        }
        if(rs==true){
          this.types.push(node);
        }
      }, next() {
          if (this.active++ > 1) this.active = 0;
          this.diyibu=true
        },
        queryPinpais:function(){
          this.$ajax.get("http://localhost:8080/api/pinpai/queryData").then(res=>{
            this.Pinpais=res.data.data;
            debugger
          }).catch(function () {

          })
        },
        nextShang(){
          this.active--;
          this.diyibu=false
        }
      },
      created:function () {

        this.queryTypeData();
      }
    }
</script>

<style scoped>

</style>
