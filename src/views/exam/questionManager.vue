<!--
<template>

        <div>
          <el-form :inline="true" :model="userform"  ref="userform" :rules="rules" label-width="100px" style="margin: 0 auto;">

              <el-upload
                      name="fileName"
                      class="avatar-uploader"
                      :action="fileServerIp + 'exam/uploadFile'"
                      :show-file-list="false"
                      :on-success="handleAvatarSuccess"
                      :before-upload="beforeAvatarUpload">
                <img v-if="userform.photoName" :src="userform.photoName" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
          </el-form>
        </div>

</template>

<script>
    import http from '../../utils/http'
    export default {
        data() {
            return {
                user: JSON.parse(sessionStorage.getItem('user')),
                userPerms: JSON.parse(sessionStorage.getItem('user')).userPerms,
                menuName: '菜单',
                sysName: '学生管理系统',
                collapsed: false,
                nickName: '', // 昵称
                userImg: '', // 用户头像
                uploadImg: '', // 上传的头像
                dialogVisible: false,
                userform: {
                    photoName: '',
                    name: '',
                    email: '',
                },
                // 表单验证
                rules: {
                    photoName: [
                        { required: true, message: '请选择头像'}
                    ],
                    name: [
                        { required: true, message: '请输入用户昵称'},
                        { min: 2, max: 10, message: '长度在 5 到 12 个字符'}
                    ],
                    email: [
                        { required: true, message: '请输入邮箱地址' },
                        { type: 'email', message: '请输入正确的邮箱地址' }
                    ]
                }
            }
        },
        methods: {
            handleopen() {
                //console.log('handleopen');
            },
            handleclose() {
                //console.log('handleclose');
            },
            handleselect: function (a, b) {
            },
            //退出登录
            logout: function () {
                var _this = this;
                this.$confirm('确认退出吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    sessionStorage.removeItem('user');
                _this.$router.push('/login');
            }).catch(() => {
                    return 'login'
                })
            },
            //折叠导航栏
            collapse:function(){
                this.collapsed=!this.collapsed;
            },
            showMenu(i,status){
                this.$refs.menuCollapsed.getElementsByClassName('submenu-hook-'+i)[0].style.display=status?'block':'none';
            },
            // 表单提交
            submitForm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        console.log('submit');
                        this.editUserInfo()
                    } else {
                        console.log('error submit!!');
                return false
            }
            })
            },
            // 重置
            resetForm(formName) {
                this.$refs[formName].resetFields();
            },
            // 个人中心信息
            personalCenter() {
                let _this = this
                _this.dialogVisible = true
                var user =  _this.user
                if(user) {
                    _this.userform.photoName = _this.fileServerIp + "exam/"+user.photoName,
                        _this.userform.name = user.username,
                        _this.userform.email = user.email
                }
            },
            // 编辑个人信息
            async editUserInfo() {
        let _this = this
        let params = {
            id: _this.user.id,
            username: _this.userform.name,
            email: _this.userform.email,
            photoName: _this.uploadImg == ''?_this.user.userImg:_this.uploadImg
        }
        let data = await http.post('user/update', params)
        if(data.data.status === 200) {
            _this.message(true,data.data.msg,'success')
            _this.dialogVisible = false
        } else {
            _this.message(true,data.data.msg,'error')
        }
    },
    handleAvatarSuccess(res, file) {
        this.uploadImg = res
        this.userform.photoName = URL.createObjectURL(file.raw);
    },
    beforeAvatarUpload(file) {

        return true;
    },
    initUserInfo() {
        let _this = this
        var usr= _this.user
        if (usr) {
            this.nickName = usr.username
            this.userImg = _this.fileServerIp+"headerImg/" + usr.photoName
        } else {
            _this.$router.push('/login');
        }
    },
    /**
     * ifshow: true/false msg: message  type: error/error/success
     */
    message(ifshow,msg,type) {
        this.$message({
            showClose: ifshow,
            message: msg,
            type: type
        })
    }
    },
    mounted() {
        this.initUserInfo()
    }
    }

</script>

<style scoped lang="scss">
  @import '~scss_vars';

  .container {
    position: absolute;
    top: 0px;
    bottom: 0px;
    width: 100%;
    .header {
      height: 60px;
      line-height: 60px;
      // margin-top: 2px;
      background: $color-primary;
      color:#fff;
      .userinfo {
        text-align: right;
        padding-right: 35px;
        float: right;
        .userinfo-inner {
          cursor: pointer;
          color:#fff;
          img {
            width: 50px;
            height: 50px;
            border-radius: 30px;
            margin: 5px 5px 10px 10px;
            float: right;
          }
        }
      }
      .logo {
        //width:230px;
        height:60px;
        font-size: 22px;
        padding-left:20px;
        padding-right:20px;
        border-color: rgba(238,241,146,0.3);
        border-right-width: 1px;
        border-right-style: solid;
        img {
          width: 40px;
          float: left;
          margin: 10px 10px 10px 18px;
        }
        .txt {
          color:#fff;
        }
      }
      .logo-width{
        width:230px;
      }
      .logo-collapse-width{
        width:60px
      }
      .tools{
        padding: 0px 23px;
        width:14px;
        height: 60px;
        line-height: 60px;
        cursor: pointer;
      }
    }
    .main {
      display: flex;
      // background: #324057;
      position: absolute;
      top: 60px;
      bottom: 0px;
      overflow: hidden;
      aside {
        flex:0 0 230px;
        width: 230px;
        .el-menu{
          height: 100%;
        }
        .el-menu-vertical-demo-main{
          width: 100%!important; // 剔除内联样式的width: 64px  (width: 64px-&ndash;&gt;这是一个bug)
        }
        .collapsed{
          width:60px;
          .item{
            position: relative;
          }
          .submenu{
            position:absolute;
            top:0px;
            left:60px;
            z-index:99999;
            height:auto;
            display:none;
          }

        }
      }
      .menu-collapsed{
        flex:0 0 60px;
        width: 60px;
      }
      .menu-expanded{
        flex:0 0 230px;
        width: 230px;
      }
      .content-container {
        // background: #f1f2f7;
        flex:1;
        // position: absolute;
        // right: 0px;
        // top: 0px;
        // bottom: 0px;
        // left: 230px;
        overflow-y: scroll;
        padding: 20px;
        .breadcrumb-container {
          //margin-bottom: 15px;
          .title {
            width: 200px;
            float: left;
            color: #475669;
          }
          .breadcrumb-inner {
            float: right;
          }
        }
        .content-wrapper {
          background-color: #fff;
          box-sizing: border-box;
        }
      }
    }
  }

  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
-->
