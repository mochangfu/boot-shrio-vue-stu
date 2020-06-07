
<template>
    <el-row>
        <!--工具条-->
        <el-col :span="64" class="toolbar" style="padding-bottom: 0px;width:100%;height:100%">
            <el-form :inline="true" :model="filters" ref="filters">
                <el-form-item     class="inputclass" prop="majorId2">
                    <el-select v-model="attr.majorId2" filterable placeholder="请选择专业"   @change="getClazzData">  <!--@change="getClazzData"-->
                        <el-option
                                v-for="item in majors"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>

                <el-form-item class="inputclass" prop="clazzId2">
                    <el-select v-model="attr.clazzId2" filterable placeholder="请 选择班级" @change="gethomeworkData" >
                        <el-option
                                v-for="item in clazzs"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>

                <el-form-item class="inputclass" prop="homeworkId">
                    <el-select v-model="attr.homeworkId2" filterable placeholder="作业">
                        <el-option
                                v-for="item in homeworks"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" class="el-icon-search" v-on:click="getFormData1">查询作业</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" class="el-icon-search" v-on:click="getFormData2">成绩统计</el-button>
                </el-form-item>
           <!--     <el-form-item>
                    <el-button type="success" class="el-icon-plus" v-on:click="showDialogForm">新增</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="danger" class="el-icon-delete" @click="batchDelete">删除</el-button>
                </el-form-item>-->
            </el-form>
        </el-col>

        <el-col>
            <el-table
                    v-if="show1"
                    v-loading="listLoading" element-loading-text="拼命加载中"
                    :data="tableData1"
                    @selection-change="handleSelectionChange1">
              <!--  <el-table-column type="selection" width="55">
                </el-table-column>-->
                <el-table-column
                        prop="homeworkName"
                        label="习题名称" sortable>
                </el-table-column>
                <el-table-column
                        prop="descrip"
                        label="说明" sortable>
                </el-table-column>
                <el-table-column
                        prop="postTime"
                        label="上传日期" sortable>
                </el-table-column>
                <el-table-column
                        prop="userName"
                        label="上传人" sortable>
                </el-table-column>

                <el-table-column
                        label="分数"
                        display>
                    <template slot-scope="scope">
                        <el-input  v-model="scope.row.score" @blur="editScore(scope.row.id,scope.row.score)" @click="readStatus=false"></el-input>
                    </template>
                </el-table-column>

                <el-table-column
                        prop="file"
                        label="文件" sortable
                        display>
                </el-table-column>

                <el-table-column label="操作" align="center" min-width="100">
                    　　　　<template slot-scope="scope">

                    　<el-button style="height: 30px;width: 75px;font-size: 10px" type="info" @click="downloadFile(scope.row.fileName)">下载</el-button>
                    　　　　
                    　　　　</template>
                    　　</el-table-column>

            </el-table>
            <el-table
                    v-if="show2"
                    v-loading="listLoading" element-loading-text="拼命加载中"
                    :data="tableData2"
                    @selection-change="handleSelectionChange1">
          <!--      <el-table-column type="selection" width="55">
                </el-table-column>-->
                <el-table-column
                        prop="score"
                        label="分数" sortable>
                </el-table-column>

                <el-table-column
                        prop="count"
                        label="人数" sortable>
                </el-table-column>

         <!--       <el-table-column
                        label="占比"
                        label="人数" sortable>
                        display>
                    <template slot-scope="scope">
                        <el-input  v-model="scope.row.score" @blur="editScore(scope.row.id,scope.row.score)" @click="readStatus=false"></el-input>
                    </template>
                </el-table-column>
-->
                <el-table-column
                        prop="percentStr"
                        label="占比" sortable>
                </el-table-column>
              <!--  <el-table-column label="操作" align="center" min-width="100">
                    　　　　<template slot-scope="scope">

                    　<el-button style="height: 30px;width: 75px;font-size: 10px" type="info" @click="downloadFile(scope.row.fileName)">下载</el-button>
                    　　　　
                    　　　　</template>
                    　　</el-table-column>-->

            </el-table>
        </el-col>

        <el-col>
            <div class="block" style="float: right;margin-right: 10px;margin-top: 10px;">
                <el-pagination
                        @size-change="handleSizeChange1"
                        @current-change="handleCurrentChange1"
                        :current-page="startPage1"
                        :page-sizes="pageSizes1"
                        :page-size="pageSize1"
                        layout="total, sizes, prev, pager, next, jumper"
                        :total="total1">
                </el-pagination>
            </div>
        </el-col>

    </el-row>
</template>

<script>
    import http from '../../utils/http'
    export default {
        data() {
            return {
                filters: {
                    keyword1: ''
                },
                user_id: JSON.parse(sessionStorage.getItem('user')).id,
                attr: {

                    name2: '',
                    majorId2: null,
                    instituteId2: null,
                    clazzId2:null,
                    homeworkId2:null,
                    desc2: '',
                    date2: '',
                    time2: '',
                    fillCnt2: '',
                    judgeCnt2: '',
                    singleCnt2: '',
                    multipleCnt2: 0,
                    fillScore2: '',
                    judgeScore2: '',
                    singleScore2: '',
                    multipleScore2: 0,
                    paperId2: ''
                },
                institutes:[],    //学院列表
                majors:[],       //专业列表
                clazzs:[],       //班级列表
                homeworkId:'',
                homeworks:[],
                uploadFile:null,//即将上传的作业
                uploadFileName:"作业文档",//即将上传的作业名称
                listLoading: false, // 加载等待
                pageSizes1: [30, 50, 80, 100],
                pageSizes2: [5, 10],
                startPage1: 1,
                startPage2: 1,
                pageSize1: 30,
                pageSize2: 5,
                total1: 0,
                total2: 0,
                tableData1: [],
                tableData2: [],
                total1: 0,
                total2: 0,
                page1: 1,
                page2: 1,
                dialogTableVisible1: false,
                dialogFormVisible1: false,
                dialogFormVisible2: false,
                innerVisible: false,
                attrIds: [], // 属性ids集合
                detailIds: [], // 属性明细ids
                attrId: '',
                editing:true,
                readStatus:true,
                show1:true ,
                show2:false

            }
        },
        methods: {
            // 查询属性
            async getFormData1 () {
        let _this = this
        _this.show1=true
            _this.show2=false
            _this.listLoading = true
        let params = {
            startPage: _this.startPage1,
            pageSize: _this.pageSize1,
            name: _this.filters.keyword1,
            institute: _this.attr.instituteId2,
            major: _this.attr.majorId2,
            clazz: _this.attr.clazzId2,
            homeworkId:_this.attr.homeworkId2
        }
        let data = await http.get('homeworkAnswer/teacherlist', params)

        if(!data.data) {
            _this.listLoading = false
            return
        }

        if (data.data.status === 200) {
            _this.tableData1 = data.data.data
            _this.total1 = data.data.total
        } else {
            _this.message(true,data.data.msg,'error')
            _this.formData1 = []
        }
        _this.listLoading = false
    },    // 查询
    async getFormData2 () {
        let _this = this
        _this.listLoading = true
            _this.show1=false
            _this.show2=true
            let params = {
                startPage: _this.startPage1,
                pageSize: _this.pageSize1,
                name: _this.filters.keyword1,
                institute: _this.attr.instituteId2,
                major: _this.attr.majorId2,
                clazz: _this.attr.clazzId2,
                homeworkId:_this.attr.homeworkId2
        }
        let data = await http.get('homeworkAnswer/score/stats', params)

        if(!data.data) {
            _this.listLoading = false
            return
        }

        if (data.data.status === 200) {
            _this.tableData2 = data.data.data
            _this.total1 = data.data.total
        } else {
            _this.message(true,data.data.msg,'error')
            _this.formData2 = []
        }
        _this.listLoading = false
    },
    // 查询属性editScore
    async editScore (id1,score1) {
        let params = {
            id:id1,
            score:score1
        }
        let data =await http.post('homeworkAnswer/edit', params)
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            this.message(true,data.data.msg,'success')
        } else {
            this.message(true,data.data.msg,'error')
        }
        this.getFormData1()
    },

    // 添加属性表单提交
    submitForm(formName) {
        this.$refs[formName].validate((valid) => {
            if (valid) {
                this.addObj()
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
    // 新增属性
    async addObj() {
        let _this = this
        let params = {
            name: _this.attr.name2,
            desc: _this.attr.desc2,
            date: _this.attr.date2,
            totalTime: _this.attr.time2,
            major: _this.attr.majorId2,
            institute: _this.attr.instituteId2,
            clazz: _this.attr.clazzId2,
            userId: _this.user_id

        }
        let data = await http.post("homework/add", params)

        if(!data.data) {
            return
        }

        if (data.data.status === 200) {
            _this.resetForm('attr')
            _this.dialogFormVisible1=false
            _this.message(true,data.data.msg,'success')
            _this.getFormData1()
        } else {
            _this.message(true,data.data.msg,'error')
        }
    },

    // 批量删除
    batchDelete () {
        if (this.attrIds.length === 0) {
            this.message(true,'请选择需要删除的数据','error')
            return
        }
        this.$confirm('此操作将删除该数据, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'error'
        }).then(() => {
            this.doDelete()
    }).catch(() => {
            this.message(true,'已取消删除','error')
    })
    },
    // 执行删除操作
    async doDelete () {
        let params = {
            ids: this.attrIds
        }
        let data =await http.post('homework/delete', params)
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            this.message(true,data.data.msg,'success')
        } else {
            this.message(true,data.data.msg,'error')
        }
        this.getFormData1()
    },
    // 操作
    downloadFile (file_name) {
        let _this = this;
        let url= _this.fileServerIp  +'file/downloadFile/'+file_name
        window.location.href=url
    },
    // 获取选中集
    handleSelectionChange1(val) {
        this.attrIds = []
        if (val) {
            val.forEach(row => {
                this.attrIds.push(row.id)
        });
        }
    },
    // 每页大小改变时触发
    handleSizeChange1 (val) {
        this.pageSize1 = val
        this.getFormData1()
    },
    // 当前页码改变时触发
    handleCurrentChange1 (val) {
        this.startPage1 = val
        this.getFormData1()
    },
    /**
     * ifshow: true/false msg: message  type: error/warning/success
     */
    message(ifshow,msg,type) {
        this.$message({
            showClose: ifshow,
            message: msg,
            type: type
        })
    },

    handleAvatarSuccess(res, file)
    {
        this.uploadFile = res,
            this.uploadFileName = res.name

    }
    ,
    beforeAvatarUpload(file)
    {
        return true;
    },
    // 显示添加用户窗口
    showDialogForm() {
        this.dialogFormVisible1 = true
        this.uploadFile = null
        this.uploadFileName ="作业文档",
            this.getInstituteData()
    },
    // 查询学院列表
    async getInstituteData () {
        let _this = this
        let data = await http.get('institute/findAllInstitute')
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            _this.institutes = data.data.data
            _this.institutes.unshift({"id":null,"name":""})
        } else {
            _this.message(true,data.data.msg,'error')
            _this.institutes = []
        }
    },

    //获取专业列表
    async getMajorData (){
        let _this = this
        let param = {
            instituteId: _this.attr.instituteId2
        }
        let data = await http.get('major/findAllMajor',param);
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            _this.majors = data.data.data
            _this.majors.unshift({"id":null,"name":""})
        } else {
            _this.message(true,data.data.msg,'error')
            _this.majors = []
        }
    },

    //获取作业列表
    async gethomeworkData (){
        let _this = this
        let param = {
            clazz: _this.attr.clazzId2
        }
        let data = await http.get('homework/list',param);
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            _this.homeworks = data.data.data
            _this.homeworks.unshift({"id":null,"name":""})
        } else {
            _this.message(true,data.data.msg,'error')
            _this.homeworks = []
        }
    },
    //获取班级列表
    async getClazzData (){
        let _this = this
        let param = {
            majorId: _this.attr.majorId2
        }
        let data = await http.get('clazz/findAllClazz',param);
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            _this.clazzs = data.data.data
            _this.clazzs.unshift({"id":null,"name":""})
        } else {
            _this.message(true,data.data.msg,'error')
            _this.clazzs = []
        }
    }
    },
    mounted() {
        this.getFormData1()
        this.getInstituteData()
        this.getMajorData()
    }
    }
</script>

<style>
    .avatar-uploader .el-upload {
        border: 0px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
        background-color: #e1e1ed;
    }

    .avatar-uploader .el-upload:hover {
        border-color: #469EFF;
    }

    .avatar-uploader-icon {
        font-size: 15px;
        color: #8c939d;
        width: 118px;
        height: 58px;
        line-height: 58px;
        text-align: center;
    }

    .inputclass{
        width: 140px;
    }

    .avatar {
        width: 70px;
        height: 0px;
        display: table;
        background-color: #00a2d4;

    }
</style>