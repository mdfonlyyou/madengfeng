<template>
  <el-form :model="ruleForm2" status-icon :rules="rules2" ref="ruleForm2" label-width="80px" class="demo-ruleForm"
           style="margin:20px;width:60%;min-width:600px;" v-loading="editloading">
    <el-form-item label="名称" prop="name" label-width="80px">
      <el-input v-model="ruleForm2.name" auto-complete="off"></el-input>
    </el-form-item>


    <el-form-item label="简介">
      <el-input type="textarea" :autosize="{ minRows: 2, maxRows: 4}"  v-model="ruleForm2.remark" placeholder="请输入内容,可为空"></el-input>
    </el-form-item>

    <el-form-item prop="url">
      <input type="hidden" v-model="ruleForm2.url" auto-complete="off"/>
    </el-form-item>

    <el-form-item label="地址">
      <el-upload
        class="avatar-uploader"
        action="http://avatarcn.oss-cn-hangzhou.aliyuncs.com"
        :data="data"
        :file-list="fileList"
        :on-success="handleAvatarSuccess"
        :on-preview="handlePreview"
        :before-upload="beforeAvatarUpload">
        <i class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm('ruleForm2')">提交</el-button>
      <!-- <el-button @click="resetForm('ruleForm2')">重置</el-button>-->
    </el-form-item>
  </el-form>
</template>

<script>
  import {getBasicById} from '../../assets/basic'
  import {editBasicById} from '../../assets/basic'
  export default {
    data() {
      return {
        data: {
          key: ''
        },
        fileList: [{name: '', url: ""}],
        editloading:false,
        buckname: 'syllabus/tmp/',
        ruleForm2: {
          name: '',
          remark: '',
          url: '',
        },
        rules2: {
          name: [
            {required: true, message: '请输入名称', trigger: 'blur'}
          ],
          url: [
            {required: true, message: '请上传文件', trigger: 'blur'}
          ]
        },
      };
    },
    methods: {
        //获得需要修改的数据
      getBasicInfos(){
          this.editloading=true
        getBasicById(this.$route.query.id).then(response => {
          this.ruleForm2.name=response.data.name;
          this.ruleForm2.remark=response.data.remark;
          this.ruleForm2.url=response.data.url;
          //显示修改的封面名称
          this.fileList[0].name = response.data.url,
            this.fileList[0].url = response.data.url;
          this.editloading=false;
        });
      },
      submitForm(formName) {
        var _this = this;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let para = {
              name: _this.ruleForm2.name,
              fk_basic_type_id:_this.$route.query.tid,
              remark: _this.ruleForm2.remark,
              url: _this.ruleForm2.url,
            };
             _this.editloading=true
            editBasicById(_this.$route.query.id,para).then(response => {
              _this.editloading=false
              _this.$router.push({path:'/basic',query: {bid: _this.$route.query.tid}});
            });
          } else {
            _this.$message.error('提交失败');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
      //上传之前
      beforeAvatarUpload(file){
        var suffix = file.name.substr(file.name.indexOf("."));
        if (suffix != '.zip') {
          this.$message.error('上传文件不是zip不是文件!');
          return;
        }
        var storeAs = this.buckname + this.guid() + suffix;
        this.data.key = storeAs

      },
      //生成UUID文件名
      guid() {
        function S4() {
          return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
        }
        return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
      },
      handleAvatarSuccess(res, file) {
        //this.imageUrl = URL.createObjectURL(file.raw);
        this.ruleForm2.url='http://avatarcn.oss-cn-hangzhou.aliyuncs.com/'+this.data.key;
      },
      handlePreview(file) {
        console.log(file);
      }
    },
    mounted() {
      this.getBasicInfos();
    }

  }
</script>

<style>
  .el-form-item__content {
    line-height: 60px;
    position: relative;
    font-size: 14px;

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
