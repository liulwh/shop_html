<template>
    <div>
      <h1>属性</h1>

      <el-button type="success" @click="addFormFlag=true">新增</el-button>

      <el-table
        :data="tableData"
        style="width: 100%"
        @row-click="getDetails"
       >

        <el-table-column
          prop="id"
          label="序号"
          width="180">
        </el-table-column>

        <el-table-column
          prop="name"
          label="名称"
          width="180">
        </el-table-column>

        <el-table-column
          prop="nameCH"
          label="中文名称">
        </el-table-column>



        <el-table-column
          prop="typeName"
          label="分类">
        </el-table-column>

        <!--<el-table-column
          prop="ord"
          label="汽车品牌">
        </el-table-column>-->

        <el-table-column
          prop="type"
          label="属性的类型"
          :formatter="fmt">
        </el-table-column>

       <!-- <el-table-item label="特殊资源">
          <el-radio-group  size="medium">
            <el-radio border label="线上品牌商赞助"></el-radio>
            <el-radio border label="线下场地免费"></el-radio>
          </el-radio-group>
        </el-table-item>-->

       <!-- <el-table-column
          prop="createDate"
          label="上架时间">
        </el-table-column>

        <el-table-column
          prop="updateDate"
          label="下架时间">
        </el-table-column>-->


        <!--<el-table-column
          prop="author"
          label="操作人">
        </el-table-column>-->


        <el-table-column
          fixed="right"
          label="操作"
          width="200">
          <template slot-scope="scope">
            <el-button type="text" size="small"
                       @click="() => updateFormFlag=true">编辑</el-button>
            <el-button type="text" v-if="scope.row.type!=3" size="small"  v-on:click="skuValueFlag=true" >维护属性value</el-button>
            <el-button type="text" size="small" v-on:click="deleteSku(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>

      </el-table>
      <el-pagination

        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        :page-sizes="sizes"
        :page-size="size"
        layout="total, sizes, prev, pager, next, jumper"
        :total="count">
      </el-pagination>


      <!--  新增的弹框   -->
      <el-dialog title="录入属性信息" :visible.sync="addFormFlag">

        <el-form :model="addAttributeForm"  label-width="80px">


          <el-form-item label="分类">
            <el-select v-model="addAttributeForm.typeId" placeholder="请选择分类">
              <el-option label="请选择" :value="-1"></el-option>
              <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item>


          <el-form-item label="属性名称" prop="name">
            <el-input v-model="addAttributeForm.name" autocomplete="off" ></el-input>
          </el-form-item>


          <el-form-item label="中文名称" prop="nameCH">
            <el-input v-model="addAttributeForm.nameCH"></el-input>
          </el-form-item>





          <el-form-item label="是否SKU" prop="isSKU">
            <el-radio v-model="addAttributeForm.isSKU" label="0">否</el-radio>
            <el-radio v-model="addAttributeForm.isSKU" label="1">是</el-radio>
          </el-form-item>

      <!--    <el-form-item label="是否删除" prop="isDel">
            <el-radio v-model="addAttributeForm.isDel" label="0">不删除</el-radio>
            <el-radio v-model="addAttributeForm.isDel" label="1">删除</el-radio>
          </el-form-item>-->

          <!--属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框-->
          <el-form-item label="属性的类型" prop="type">
            <el-radio v-model="addAttributeForm.type" label="0">下拉框</el-radio>
            <el-radio v-model="addAttributeForm.type" label="1">单选框</el-radio>
            <el-radio v-model="addAttributeForm.type" label="2">复选框</el-radio>
            <el-radio v-model="addAttributeForm.type" label="3">输入框</el-radio>
          </el-form-item>

          <!--<el-form-item label="操作人" prop="author">
            <el-input v-model="addAttributeForm.author"></el-input>
          </el-form-item>-->


        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="addFormFlag = false">取 消</el-button>
          <el-button type="primary" @click="addForm">确 定</el-button>
        </div>
      </el-dialog>


      <!--  修改的弹框   -->
      <el-dialog title="修改属性信息" :visible.sync="updateFormFlag">

        <el-form :model="updateAttributeForm"  label-width="100px">

          <el-form-item label="属性名称" prop="name">
            <el-input v-model="updateAttributeForm.name" autocomplete="off" ></el-input>
          </el-form-item>


          <el-form-item label="中文名称" prop="nameCH">
            <el-input v-model="updateAttributeForm.nameCH"></el-input>
          </el-form-item>

          <el-form-item label="分类">
            <el-select v-model="updateAttributeForm.typeId" placeholder="请选择分类">
              <el-option label="请选择" :value="-1"></el-option>
              <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="是否SKU" prop="isSKU">
            <el-radio v-model="updateAttributeForm.isSKU" :label="0">否</el-radio>
            <el-radio v-model="updateAttributeForm.isSKU" :label="1">是</el-radio>
          </el-form-item>

       <!--   <el-form-item label="是否删除" prop="isDel">
            <el-radio v-model="updateAttributeForm.isDel" :label="0">不删除</el-radio>
            <el-radio v-model="updateAttributeForm.isDel" :label="1">删除</el-radio>
          </el-form-item>-->

          <!--属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框-->
          <el-form-item label="属性的类型" prop="type">
            <!--:label  表示获取选项的value-->
            <el-radio v-model="updateAttributeForm.type" :label="0">下拉框</el-radio>
            <el-radio v-model="updateAttributeForm.type" :label="1">单选框</el-radio>
            <el-radio v-model="updateAttributeForm.type" :label="2">复选框</el-radio>
            <el-radio v-model="updateAttributeForm.type" :label="3">输入框</el-radio>
          </el-form-item>

         <!-- <el-form-item label="操作人" prop="author">
            <el-input v-model="updateAttributeForm.author"></el-input>
          </el-form-item>-->


        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="updateFormFlag = false">取 消</el-button>
          <el-button type="primary" @click="updateForm">确 定</el-button>
        </div>
      </el-dialog>


      <!--  属性值维护的弹框   -->

      <div>

      <el-dialog title="修改属性值信息" :visible.sync="skuValueFlag">
        <el-button type="success" @click="addSkuValueFormFlag=true">新增</el-button>
        <el-table
          :data="AttrValueTableData"
          style="width: 100%"
          @row-click="getSkuVaDeta">

          <el-table-column property="name" label="属性" width="150"></el-table-column>
          <el-table-column property="nameCH" label="属性值" width="200"></el-table-column>

          <el-table-column
            fixed="right"
            label="操作"
            width="300">
            <template slot-scope="scope">
              <el-button type="text" size="small"
                         @click="() => updateSkuVaFlag=true">编辑</el-button>
              <el-button type="danger" size="small" v-on:click="deleteSkuValue(scope.row.id)">删除</el-button>
            </template>
          </el-table-column>

        </el-table>

        <el-pagination

          @current-change="handleSkuVCurrentChange"
          @size-change="handleSkuVSizeChangee"
          :page-sizes="vsizes"
          :page-size="vsize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="vcount">
        </el-pagination>



      </el-dialog>


        <!--  新增的弹框   -->
        <el-dialog title="录入属性信息"  :visible.sync="addSkuValueFormFlag">

          <el-form :model="addSkuValue"   label-width="80px">

            <el-form-item label="英文名称" prop="name">
              <el-input v-model="addSkuValue.name"  ></el-input>
            </el-form-item>
            <el-form-item label="中文名称" prop="nameCH">
              <el-input v-model="addSkuValue.nameCH"></el-input>
            </el-form-item>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="addSkuValueFormFlag = false">取 消</el-button>
            <el-button type="primary" @click="addSkuValueForm">确 定</el-button>
          </div>
        </el-dialog>


        <!--  修改的弹框   -->
        <el-dialog title="修改属性信息"  :visible.sync="updateSkuVaFlag">

          <el-form :model="updateSkuValueForm"   label-width="80px">

            <el-form-item label="英文名称" prop="name">
              <el-input v-model="updateSkuValueForm.name"  ></el-input>
            </el-form-item>
            <el-form-item label="中文名称" prop="nameCH">
              <el-input v-model="updateSkuValueForm.nameCH"></el-input>
            </el-form-item>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="updateSkuVaFlag = false">取 消</el-button>
            <el-button type="primary" @click="updateSkuValue">确 定</el-button>
          </div>
        </el-dialog>


      </div>
    </div>
</template>

<script>
    export default {
        name: "Skukey",
      data(){
          return{
            tableData:[],
            sizes:[2,3,5,10],
            size:2,
            start:1,
            count:0,

            vsizes:[2,3,5,10],
            vsize:2,
            vstart:1,
            vcount:0,
            vid:"",

            /*新增*/
            addFormFlag:false,
            addAttributeForm:{
              name:"",
              nameCH:"",
              typeId:"-1",
              type:[],
              isSKU:"",
              isDel:"0",
              author:""
            },
            ajaxTypeData:[],
            typeName:"",
            types:[
              /*{"id":3,name:"商品/电子商品/手机"},
              {"id":5,name:"商品/衣服/上衣"},
              {"id":14,name:"商品/汽车/轿车"},
              {"id":15,name:"商品/汽车/大奔"},*/],

            updateFormFlag:false,
            updateAttributeForm:{
              name:"",
              nameCH:"",
              typeId:"",
              type:[],
              isSKU:"",
              isDel:"",
              author:""
            },
            updateSkuValueForm:{
              name:"",
              nameCH:"",
              typeId:""
            },
            skuValueFlag:false,
            AttrValueTableData:[],
            addSkuValueFormFlag:false,
            addSkuValue:{
              skuKeyId:""
            },
            updateSkuVaFlag:false
          }
      },
      methods:{
        queryData:function () {
          var athis=this;
          this.$ajax.get("http://localhost:8080/api/skukey/querySkuKeyData?start="+this.start+"&size="+this.size).then(function (res) {
            athis.tableData=res.data.data.list;
            athis.count=res.data.data.count;
            console.log(res.data.data.list);

          }).catch(function () {

            alert("查询失败")
          })
        },deleteSku:function (id) {

          this.$ajax.delete("http://localhost:8080/api/skukey/delSkuKey?id="+id).then(function () {
            alert("删除成功");
            history.go(0);
          }).catch(function () {

          })
        },
        addForm:function () {
          var athis=this;
          this.$ajax.post("http://localhost:8080/api/skukey/addSkuKey",this.$qs.stringify(this.addAttributeForm)).then(function () {
            athis.addFormFlag = false;
            athis.queryData(1);
          }).catch(function () {

          })
        }, getDetails(row){
          var athis = this;

          athis.updateAttributeForm.id=row.id;
          athis.updateAttributeForm.name=row.name;
          athis.updateAttributeForm.nameCH=row.nameCH;
          athis.updateAttributeForm.typeId=row.typeId;
          athis.updateAttributeForm.type=row.type;
          athis.updateAttributeForm.isSKU=row.isSKU;
          athis.updateAttributeForm.isDel=row.isDel;
          athis.updateAttributeForm.author=row.author;
          athis.querySkuV(athis.updateAttributeForm.id);
          console.log("属性修改"+JSON.stringify(row))//此时就能拿到整行的信息
        },getSkuVaDeta(row){
          var athis = this;
          athis.updateSkuValueForm.id=row.id;
          athis.updateSkuValueForm.skuKeyId=row.skuKeyId;
          athis.updateSkuValueForm.name=row.name;
          athis.updateSkuValueForm.nameCH=row.nameCH;
        },
        updateForm:function (rs) {
          console.log("ssss"+rs);
          var a =this;
          this.$ajax.post("http://localhost:8080/api/skukey/updateSkuKey",this.$qs.stringify(this.updateAttributeForm)).then(res=>{
            this.updateFormFlag=false;
            alert("修改成功");
            a.queryData(1);

          }).catch(err=>console.log(err))

        },updateSkuValue:function (rs) {
          console.log("ssss"+rs);
          var athis =this;
          this.$ajax.post("http://localhost:8080/api/SkuValue/updateSkuValue",this.$qs.stringify(this.updateSkuValueForm)).then(res=>{
            this.updateSkuVaFlag=false;
              alert("修改成功");
            athis.querySkuV();
          }).catch(err=>console.log(err))
        }
        ,fmt:function(a,b,c,d){
          if(c==0){
            return "下拉框";
          }
          if(c==1){
            return "单选框";
          }if(c==2){
            return "复选框";
          }if(c==3){
            return "输入框";
          }
        },querySkuV:function (id) {
          var athis=this;
          //console.log(id);
         // http://localhost:8080/api/skukey/querySkuKeyData?start="+this.start+"&size="+this.size
          this.$ajax.get("http://localhost:8080/api/SkuValue/queryData?skuKeyId="+athis.updateAttributeForm.id+"&size="+this.vsize+"&start="+this.vstart).then(function (res) {
             console.log(res);
            athis.AttrValueTableData=res.data.data.list;
            athis.vcount=res.data.data.count;
          }).catch(function () {
            alert("查询skuV失败");
          })

        },addSkuValueForm:function () {
          var athis=this;
         this.addSkuValue.skuKeyId=athis.updateAttributeForm.id;
          //alert(athis.updateAttributeForm.id);
          this.$ajax.post("http://localhost:8080/api/SkuValue/addSkuValue",this.$qs.stringify(this.addSkuValue)).then(function () {

            athis.addSkuValueFormFlag = false;
             alert("新增成功");
            athis.addSkuValue={};
            athis.querySkuV();
          }).catch(function () {
            alert("新增skuV失败");
          })
        }
        ,deleteSkuValue:function (id) {
          var athis=this;
          this.$ajax.delete("http://localhost:8080/api/SkuValue/deleteSkuValue?id="+id).then(function () {

            alert("删除成功");
            athis.querySkuV();
          }).catch(function () {
            alert("删除skuV失败");
          })
        },queryTypeData:function(){

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
        }
        ,handleCurrentChange:function(start){ //跳转页面
          console.log(start);
          this.start=start;
          this.queryData(start);
        },handleSizeChange:function(size){ //跳转页面
          this.size=size;
          this.queryData(1);
        },handleSkuVCurrentChange:function(vstart){ //跳转页面
          console.log(vstart);
          this.vstart=vstart;
          this.querySkuV(vstart);
        },handleSkuVSizeChangee:function(vsize){ //跳转页面
          this.vsize=vsize;
          this.querySkuV(1);
        }

      },created:function () {
        //请求数据
        this.queryData(1,2);
        //查询类型
         this.queryTypeData();




      }
    }
</script>

<style scoped>

</style>
