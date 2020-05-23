
<template>
    <el-row>
        <!--工具条-->
        <el-col :span="64" class="toolbar" style="padding-bottom: 0px;width:100%;height:100%">
            <el-form :inline="true" :model="filters" ref="filters">
                <el-form-item  class="inputclass">
                    <el-input    placeholder="习题名称" v-model="filters.keyword1"></el-input>
                </el-form-item>

                <el-form-item style="display: none"    class="inputclass"  prop="instituteId2">
                    <el-select   v-model="attr.instituteId2" filterable placeholder="请选择学院" ><!--@change="getMajorData"-->
                        <el-option
                                v-for="item in institutes"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
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
                    <el-select v-model="attr.clazzId2" filterable placeholder="请 选择班级">
                        <el-option
                                v-for="item in clazzs"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" class="el-icon-search" v-on:click="getFormData1">查询</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="success" class="el-icon-plus" v-on:click="showDialogForm">新增</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="danger" class="el-icon-delete" @click="batchDelete">删除</el-button>
                </el-form-item>
            </el-form>
        </el-col>

        <el-col>
            <el-table
                    v-loading="listLoading" element-loading-text="拼命加载中"
                    :data="tableData1"
                    @selection-change="handleSelectionChange1">
                <el-table-column type="selection" width="55">
                </el-table-column>
                <el-table-column
                        prop="name"
                        label="习题名称" sortable>
                </el-table-column>
                <el-table-column
                        prop="desc"
                        label="说明" sortable>
                </el-table-column>
                <el-table-column
                        prop="uploadTime"
                        label="上传日期" sortable>
                </el-table-column>
                <el-table-column

                        prop="userName"
                        label="上传人" sortable>
                </el-table-column>
                <el-table-column
                        prop="fileName"
                        label="文件名" sortable
                            display>
                </el-table-column>
                <el-table-column label="操作" align="center" min-width="100">
                    　　　　<template slot-scope="scope">
                    　　　　　　<el-button type="info" @click="downloadFile(scope.row.fileName)">下载</el-button>
                    　　　　
                    　　　　</template>
                    　　</el-table-column>

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


        <!-- 新增 -->
        <el-dialog title="新增作业" :visible.sync="dialogFormVisible1">
            <div style="width:60%;margin: 0 auto">
                <el-form ref="attr" :model="attr" :inline="false" label-width="90px" class="demo-ruleForm">

                    <el-form-item style="display: none"  label="学院" prop="instituteId2">
                        <el-select v-model="attr.instituteId2" filterable placeholder="请选择" @change="getMajorData">
                            <el-option
                                    v-for="item in institutes"
                                    :key="item.id"
                                    :label="item.name"
                                    :value="item.id">
                            </el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="专业" prop="majorId2" :rules="[{ required: true }]">
                        <el-select v-model="attr.majorId2" filterable placeholder="请选择" @change="getClazzData">
                            <el-option
                                    v-for="item in majors"
                                    :key="item.id"
                                    :label="item.name"
                                    :value="item.id">
                            </el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="班级"   prop="clazzId2" :rules="[{ required: true }]">
                        <el-select v-model="attr.clazzId2" filterable placeholder="请选择班级">
                            <el-option
                                    v-for="item in clazzs"
                                    :key="item.id"
                                    :label="item.name"
                                    :value="item.id">
                            </el-option>
                        </el-select>
                    </el-form-item>

                    <el-form-item label="作业名称" prop="name2" :rules="[{ required: true, message: '请输入作业名称', trigger: 'blur' }]">
                        <el-input  type="text" v-model="attr.name2" placeholder="作业名称" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="作业介绍" prop="desc2">
                        <el-input  type="text" v-model="attr.desc2" placeholder="作业介绍" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="上交日期" prop="date2" :rules="[{  message: '请输入作业日期', trigger: 'blur' }]">
                        <el-input  type="date" v-model="attr.date2" placeholder="作业日期" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item>

                        <el-upload
                                name="fileName"
                                class="avatar-uploader"
                                :action="fileServerIp + 'file/uploadFile'"
                                :show-file-list="false"
                                :data="{'userId':user_id}"
                                :on-success="handleAvatarSuccess"
                                :before-upload="beforeAvatarUpload">

                            <img label=" " v-if="1" class="avatar">
                            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                            <a style="background-color: white  ;color: black;"> {{uploadFileName}}</a>
                        </el-upload>
                    </el-form-item>
                </el-form>
            </div>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible1 = false">取 消</el-button>
                <el-button @click="resetForm('attr')">重置</el-button>
                <el-button type="primary" @click="submitForm('attr')">确 定</el-button>
            </div>
        </el-dialog>


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
                attrId: ''
            }
        },
        methods: {
            // 查询属性
            async getFormData1 () {
        let _this = this
        _this.listLoading = true
        let params = {
            startPage: _this.startPage1,
            pageSize: _this.pageSize1,
            name: _this.filters.keyword1,
            instituteId: _this.attr.instituteId2,
            majorId: _this.attr.majorId2,
            clazzId: _this.attr.clazzId2
        }
        let data = await http.get('fileRecord/exam/list', params)

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
            examCourse: _this.attr.name2,
            examDesc: _this.attr.desc2,
            examDate: _this.attr.date2,
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
        let data =await http.post('fileRecord/delete', params)
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
    // 执行删除操作
     downloadFile (file_name) {
        let _this = this;
        let url= _this.fileServerIp  +'exam/downloadFile?fileName='+file_name
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
        font-size: 20px;
        color: #8c939d;
        width: 128px;
        height: 58px;
        line-height: 58px;
        text-align: center;
    }

    .inputclass{
        width: 150px;
    }

    .avatar {
        width: 80px;
        height: 0px;
        display: table;
        background-color: #00a2d4;

    }
</style>