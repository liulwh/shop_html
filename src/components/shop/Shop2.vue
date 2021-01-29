<template>


  <div>
    <el-steps :active="active" finish-status="success">
      <el-step title="填写商品信息"></el-step>
      <el-step title="填写商品属性"></el-step>
    </el-steps>
    <div align="center" v-if="!diyibu">
      <el-form :model="addShop" ref="addAttrForm"  label-width="80px">


        <el-form-item style="width:400px" label="商品名称" prop="name">
          <el-input v-model="addShop.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item align="center" style="width:400px" label="商品标题" prop="nameCH">
          <el-input v-model="addShop.title"></el-input>
        </el-form-item>

        <el-form-item align="center" label="品牌分类" prop="brandId">
          <el-select v-model="addShop.brandId" placeholder="请选择">
            <el-option v-for="item in Pinpais" :key="item.id" :label="item.name" :value="item.id"></el-option>
          </el-select>
        </el-form-item>

     <!--   <el-form-item label="品牌" prop="bandId">
          <el-select v-model="productForm.bandId" placeholder="请选择">
            <el-option v-for="b in brandDatas" :key="b.id" :label="b.name" :value="b.id"></el-option>
          </el-select>
        </el-form-item>-->


        <el-form-item label="图片">
          <el-upload
            class="upload-demo"
            action="http://localhost:8080/uploadFile/uploadFile"
            :on-success="imgCallBack"
            name="file"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">不超过500kb</div>
          </el-upload>
        </el-form-item>


        <el-form-item style="width:400px"  label="商品介绍" prop="typeId">
          <el-input type="textarea" v-model="addShop.productdecs"></el-input>
        </el-form-item>
        <el-form-item align="center" style="width:400px" label="价格" prop="nameCH">
          <el-input v-model="addShop.price"></el-input>
        </el-form-item>
        <el-form-item align="center" style="width:400px" label="库存" prop="nameCH">
          <el-input v-model="addShop.stocks"></el-input>
        </el-form-item>
        <el-form-item align="center" style="width:400px" label="排序" prop="nameCH">
          <el-input v-model="addShop.sortNum"></el-input>
        </el-form-item>
      </el-form>
      <el-button style="margin-top: 12px;" @click="next">下一步</el-button>
    </div>



    <div v-if="diyibu">


      <el-form :model="productForm2" label-width="100px" class="demo-ruleForm">

        <el-form-item label="商品分类">
          <el-select v-model="addShop.typeId" placeholder="请选择分类"  v-on:change="querySkuKeyByTypeId">
            <el-option label="请选择" :value="-1"></el-option>
            <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
          </el-select>
        </el-form-item>



        <!--商品 sku 属性 规格-->
        <el-form-item v-if="SKUData.length>0" label="商品规格" prop="name" >


          <el-form-item v-for="a in  SKUData" :key="a.id" :label="a.nameCH">

            <!--  0 下拉框     1 单选框      2  复选框   3  输入框  -->
            <el-input v-if="a.type==3"></el-input>

            <el-select v-if="a.type==0"  placeholder="请选择" >
              <el-option v-for="b in a.values" :key="b.id"  :label="b.nameCH" value="b.id"></el-option>
            </el-select>

            <el-radio-group  v-if="a.type==1"  >
              <el-radio v-for="b in a.values" :key="b.id" :label="b.nameCH"></el-radio>
            </el-radio-group>


            <el-checkbox-group v-model="a.ckValues"  v-if="a.type==2"  @change="skuChang">
              <el-checkbox v-for="b in a.values" :key="b.id" :label="b.nameCH" name="type"></el-checkbox>
            </el-checkbox-group>

          </el-form-item>


        </el-form-item>


        <!-- 笛卡尔积 表格-->
        <el-table
          v-if="tableShow"
          :data="tableSkuData"
          style="width: 100%">
          <!--   动态展示列头  sku属性中文名 -->
          <el-table-column v-for="c in cols" :key="c.id" :label="c.nameCH" :prop="c.name">

          </el-table-column>

          <el-table-column
            label="库存"
            width="180">

            <template slot-scope="scope">
              <el-input v-model="scope.row.stocks"></el-input>
            </template>

          </el-table-column>
          <el-table-column
            label="价格"
            width="180">
            <template slot-scope="scope">
              <el-input v-model="scope.row.price"></el-input>
            </template>
          </el-table-column>
        </el-table>





        <!--  商品参数 非sku 属性-->
        <el-form-item v-if="attData.length>0" label="商品参数" prop="name">

          <el-form-item v-for="a in  attData" :key="a.id" :label="a.nameCH">

            <!--  0 下拉框     1 单选框      2  复选框   3  输入框  -->
            <el-input v-if="a.type==3" v-model="a.ckValues"></el-input>

            <el-select v-if="a.type==0"  v-model="a.ckValues" placeholder="请选择">
              <el-option v-for="b in a.values" :key="b.id"  :label="b.nameCH" :value="b.id" ></el-option>
            </el-select>

            <el-radio-group v-model="a.ckValues"  v-if="a.type==1" >
              <el-radio v-for="b in a.values" :key="b.id" :label="b.nameCH"></el-radio>
            </el-radio-group>


            <el-checkbox-group v-model="a.ckValues" v-if="a.type==2" >
              <el-checkbox v-for="b in a.values" :key="b.id" :label="b.nameCH" name="type"></el-checkbox>
            </el-checkbox-group>



          </el-form-item>

        </el-form-item>

        <el-button style="margin-top: 12px;" v-on:click="addShopFrom" >增加</el-button>


      </el-form>

    </div>


  </div>




</template>

<script>
  import ajax from 'axios'
  import qs   from  'qs'
    export default {
        name: "Shop",
      data(){
          return{
            active: 0,
            addShop:{
              brandId:""

            },
            types:[],
            brands:[],
            num: 1,
            diyibu:false,
            Pinpais:[],
            ajaxTypeData:[],
            typeName:"",
            ss:[],
            bb:"",
            shuru:"",
            xiala:"",

            productForm2:{
            },
            attData:[],   //非sku的属性数据
            SKUData:[], //sku属性数据
            ckValues:[],
            skuCK:[], //确定sku复选框绑定的变量名
            tableShow:false, //sku的table是否显示
            cols:[],//skutable的动态表头
            tableSkuData:[],//skutable的动态表头对应的表格数据


          }
    },methods:{

        /* 笛卡尔积    */
        calcDescartes:function(array) {
          if (array.length < 2) return array[0] || [];
          return [].reduce.call(array, function (col, set) {
            var res = [];
            col.forEach(function (c) {
              set.forEach(function (s) {
                var t = [].concat(Array.isArray(c) ? c : [c]);
                t.push(s);
                res.push(t);
              })
            });
            return res;
          });
        },
        skuChang:function(){
          //笛卡尔积的参数
          let  dkej=[];
          //清空表格数据
          this.tableSkuData=[];
          //清空动态表头数据
          this.cols=[];
          // console.log(this.SKUData);
          //判断是否要生成笛卡尔积
          let flag=true;
          //遍历sku所有数据
          for (let i = 0; i <this.SKUData.length ; i++) {
            //将sku属性 放入动态表头中
            this.cols.push({"id":this.SKUData[i].id,"nameCH":this.SKUData[i].nameCH,"name":this.SKUData[i].name});

            //将此属性选中的选项值放入笛卡尔积参数中
            //得到当前sku选中的值  颜色  选中的值 [红色,绿色]    尺寸 [l,x]
            dkej.push(this.SKUData[i].ckValues);
            //判断此sku的复选框是否为选中
            if(this.SKUData[i].ckValues.length==0){
              flag=false; // 不能生成笛卡尔积
              break;
            }
          }
          if(flag==true){

            //alert("生成笛卡尔积");
            //生成table的数据  [[],[],[]]   [{},{},{}]
            let  datas=this.calcDescartes(dkej);
            //遍历数据  [[红色,16g],[绿色,16g],[黑色，16g]]
            for (let i = 0; i <datas.length ; i++) {
              //遍历笛卡尔积 的每一项   [红色,16g]  cols:[{"id":1,"name": ,"nameCH"}]

              let  jsonData={}; //笛卡尔积 转为json的对象
              for (let j = 0; j <datas[i].length ; j++) {
                //获取数据的key
                let  key=this.cols[j].name;
                jsonData[key]=datas[i][j]

              }
              this.tableSkuData.push(jsonData);
            }
            console.log(this.tableSkuData);
            console.log(datas);


          }
          this.tableShow=flag;
        },

        imgCallBack:function(response, file, fileList){ //图片上传的回调函数
          // 赋值
          console.log(response);
          this.addShop.imgPath=response.data;

         // console.log("修改之后的回显"+this.updateBrandForm.imgpath)
        }
        ,
          querySkuKeyByTypeId:function(typeId){
            var athis=this;
            athis.SKUData=[];
            athis.attData=[];
            //  alert(typeId);

            this.$ajax.get("http://192.168.1.135:8080/api/skukey/querySkukeyByTypeId?typeId="+typeId).then(function (res) {


              let  attrDatas=res.data.data.data;
              console.log(attrDatas);

              if(attrDatas.length>0){
                //var athis=this;
                //初始化  attData      SKUData
                for (let i = 0; i <attrDatas.length ; i++) {
                  //判断是否为sku属性 0 不是 1 不是
                  if(attrDatas[i].isSKU==0){

                    if(attrDatas[i].type!=3){
                      attrDatas[i].ckValues=[];
                      //发起请求 查询此属性对应的选项值
                      //debugger;
                      ajax.get("http://localhost:8080/api/SkuValue/querySkuValueBySkuKeyId?skuKeyId="+attrDatas[i].id).then(res=>{
                        console.log(res);
                        attrDatas[i].values=res.data.data.data;
                        athis.attData.push(attrDatas[i]);
                      })
                    }else{
                      athis.attData.push(attrDatas[i]);
                    }

                  }else{
                    if(attrDatas[i].type!=3){

                      //发起请求 查询此属性对应的选项值
                      ajax.get("http://localhost:8080/api/SkuValue/querySkuValueBySkuKeyId?skuKeyId="+attrDatas[i].id).then(res=>{
                        attrDatas[i].values=res.data.data.data;
                        attrDatas[i].ckValues=[];
                        athis.SKUData.push(attrDatas[i]);


                      })
                    }else{
                      attrDatas[i].ckValues=[];
                      athis.SKUData.push(attrDatas[i]);
                    }

                  }
                }
              }else{
                athis.SKUData=[];
                athis.attData=[];
              }



            }).catch(function () {

              alert("根据分类查询商品属性失败");
            })


          },
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
      },

        next() {
          if (this.active++ > 1) this.active = 0;
          this.diyibu=true
        },
        queryPinpais:function(){
          this.$ajax.get("http://localhost:8080/api/brand/queryBrandData").then(res=>{
            this.Pinpais=res.data.data.data;
            console.log(this.Pinpais);

          }).catch(function () {

          })
        },
        nextShang(){
          this.active--;
          this.diyibu=false
        },

        addShopFrom:function(){


          //  this.addShop  第一页里面表单的数据  商品的基本信息
          //console.log(this.addShop);

          let attrs=[];
          for (let i = 0; i <this.attData.length ; i++) {

            let  attData={};
            attData[this.attData[i].name]=this.attData[i].ckValues;
            attrs.push(attData);
          }

          this.addShop.attr=JSON.stringify(attrs);
          this.addShop.sku=JSON.stringify(this.tableSkuData);
          console.log(this.$qs.stringify(this.addShop));

          console.log(this.addShop.attr);
          debugger;
          var athis=this;
          athis.$ajax.post("http://localhost:8080/api/shop/addShop",athis.$qs.stringify(this.addShop)).then(function () {


          }).catch(function () {

          })


        }
      },
      created:function () {

        this.queryTypeData();

        this.queryPinpais();
      }
    }
</script>

<style scoped>

</style>
