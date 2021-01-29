<template>
    <div>


      <

      <div class="login-container">
        <el-form ref="form" :model="form" label-width="80px" class="login-form">
          <h2 class="login-title">主页面</h2>
          <el-form-item label="用户名">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="密码">
            <el-input v-model="form.password"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="login('form')">登录</el-button>
            <el-button type="primary" @click="addUserFlag=true">注册</el-button>
          </el-form-item>
        </el-form>
      </div>





      <el-dialog title="注册信息" :visible.sync="addUserFlag">

        <el-form :model="addUserForm"  ref="addUserForm"  label-width="80px">
          <el-form-item style="width:400px" label="账号" >
            <el-input v-model="addUserForm.name" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item style="width:400px" label="真实姓名" >
            <el-input v-model="addUserForm.realName" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item style="width:400px" label="密码" >
            <el-input  v-model="addUserForm.password" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="性别">
            <el-radio-group v-model="addUserForm.sex">
              <el-radio label="0">男</el-radio>
              <el-radio label="1">女</el-radio>
              <el-radio label="2">保密</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item style="width:400px" label="手机号" prop="name">
            <el-input  v-model="addUserForm.phone" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item style="width:400px" label="邮箱" prop="name">
            <el-input  v-model="addUserForm.email" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item style="width:400px" label="身份证" prop="name">
            <el-input  v-model="addUserForm.idCard" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item style="width:400px" label="生日">
            <el-form-item prop="date1">
              <el-date-picker type="date" placeholder="选择日期" v-model="addUserForm.birthday" style="width: 100%;"></el-date-picker>
            </el-form-item>
          </el-form-item>



          <el-form-item label="LOGO">
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

          <el-form-item label="学历" prop="region">
            <el-select v-model="addUserForm.eduId" placeholder="请选择活动区域">
              <el-option label="小学" value="1"></el-option>
              <el-option label="大学" value="2"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="部门" prop="region">
            <el-select v-model="addUserForm.deptId" placeholder="请选择活动区域">
              <el-option label="小部门" value="1"></el-option>
              <el-option label="大部门" value="2"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="addUserFlag = false">取 消</el-button>
          <el-button type="primary" @click="addUser">确 定</el-button>
        </div>

      </el-dialog>

    </div>
</template>

<script>
    export default {
        name: "Login",
      data() {
        return {
          form: {
            username: "",
            password: ""
          },
          addUserForm:{
            imgUrl:""
          },
          addUserFlag:false
        };
      },
      methods: {
        login:function(form){
          this.$refs[form].validate((valid) => {
            if (valid) {
              this.$ajax.post("http://localhost:8080/api/user/loginUser",this.$qs.stringify(this.form)).then(res=> {
                 console.log(res)
                if (res.data.code==500){
                  this.$message.warning(res.data.msg);
                }else if(res.data.code==400){
                  this.$message.warning("密码错误");
                }else {
                  this.$message.success("登陆成功");
                  this.$router.replace('/test')
                }
              })
            } else {
              return false;
            }
          });
        },
        addUser:function(){
          this.$ajax.post("http://localhost:8080/api/user/addUser",this.$qs.stringify(this.addUserForm)).then(res=> {
            console.log(res.data);
            console.log(res.data.code)
            if (res.data.code==500){
              this.$message.warning("账号已存在");
            }
            else {
              this.$message.success("注册成功");
              this.zhuceShow=false;
            }


          })
        }
        ,imgCallBack:function(response, file, fileList){ //图片上传的回调函数
          // 赋值
          console.log(response);
          this.addUserForm.imgUrl=response.data;

        }
      }
    }
</script>

<style scoped>
  .login-form {
    width: 350px;
    margin: 160px auto; /* 上下间距160px，左右自动居中*/
    background-color: rgb(255, 255, 255); /* 透明背景色 */
    padding: 30px;
    border-radius: 20px; /* 圆角 */
  }

  /* 背景 */
  .login-container {
    position: absolute;
    width: 100%;
    height: 100%;

  }

  /* 标题 */
  .login-title {
    color: #303133;
    text-align: center;
  }
</style>
