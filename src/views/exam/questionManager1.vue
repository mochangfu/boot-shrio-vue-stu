
<template>
    <el-row>
        <!--工具条-->
        <el-col :span="64" class="toolbar" style="padding-bottom: 0px;with:100%;height: 60px;">
            <el-form :inline="true" :model="fileForm" ref="fileForm">
                <el-form-item>
                    <el-upload
                            name="fileName"
                            class="avatar-uploader"
                            :action="fileServerIp + 'exam/uploadFile'"
                            :show-file-list="false"
                            :on-success="handleAvatarSuccess"
                            :before-upload="beforeAvatarUpload">

                        <img label=" " v-if="1" class="avatar">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                        <a  v-model="fileForm.name">上传习题</a>
                    </el-upload>
                </el-form-item>
                <el-form-item>
                    <el-button type="success" class="el-icon-plus" v-on:click="sale">保存</el-button>
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
                        prop="examCourse"
                        label="课程名称" sortable>
                </el-table-column>
                <el-table-column
                        prop="major"
                        label="专业" sortable>
                </el-table-column>
                <el-table-column
                        prop="institute"
                        label="学院" sortable>
                </el-table-column>
                <el-table-column
                        prop="examDate"
                        label="考试日期" sortable>
                </el-table-column>
                <el-table-column
                        prop="totalTime"
                        label="考试时长(分钟)" sortable>
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
        <el-dialog title="新增考试" :visible.sync="dialogFormVisible1">
            <div style="width:60%;margin: 0 auto">
                <el-form ref="attr" :model="attr" :inline="false" label-width="90px" class="demo-ruleForm">

                    <el-form-item label="学院" prop="instituteId2">
                        <el-select v-model="attr.instituteId2" filterable placeholder="请选择" @change="getMajorData">
                            <el-option
                                    v-for="item in institutes"
                                    :key="item.id"
                                    :label="item.name"
                                    :value="item.id">
                            </el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="专业" prop="majorId2">
                        <el-select v-model="attr.majorId2" filterable placeholder="请选择">
                            <el-option
                                    v-for="item in majors"
                                    :key="item.id"
                                    :label="item.name"
                                    :value="item.id">
                            </el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="考试名称" prop="name2" :rules="[{ required: true, message: '请输入考试名称', trigger: 'blur' }]">
                        <el-input  type="text" v-model="attr.name2" placeholder="请输入考试名称" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="考试介绍" prop="desc2">
                        <el-input  type="text" v-model="attr.desc2" placeholder="请输入考试介绍" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="考试日期" prop="date2" :rules="[{ required: true, message: '请输入考试日期', trigger: 'blur' }]">
                        <el-input  type="date" v-model="attr.date2" placeholder="请输入考试日期" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="考试时长(分钟)" prop="time2" :rules="[{ required: true, message: '请输入考试时长', trigger: 'blur' },{validator: 'regexp', pattern: /^[1-9]\d*$/, message: '考试时长只能是整数', trigger: 'change,blur'}]">
                        <el-input  type="number" v-model="attr.time2" placeholder="请输入考试时长" auto-complete="off"></el-input>
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
                fileForm: {
                    name: '',
                    fileName: ''
                },
                attr: {
                    name2: '',
                    majorId2: '',
                    instituteId2: '',
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
            name: _this.filters.keyword1
        }
        let data = await http.get('exam/list', params)

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
                this.addInstitute()
            } else {
                console.log('error submit!!');
        return false
    }
    })
    },


    // 执行删除操作
    async doDelete () {
        let params = {
            ids: this.attrIds
        }
        let data =await http.post('exam/delete', params)
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

    handleAvatarSuccess(res, file)
    {
        this.fileForm.fileName = res.fileName
        this.fileForm.name = res.name;

    }
    ,
    beforeAvatarUpload(file)
    {
        return true;
    }
    ,
    initPage()
    {
        let _this = this;
        var usr = _this.user

    },
    async saveFile()
    {
        let _this = this
        let params = {
            classId: _this.file.classId,
            courseId: _this.file.courseId,
            fileName: _this.file.fileName,
            fileName: _this.file.name,
        }
        let data = await
        http.post('file/save', params)
        if (data.data.status === 200) {
            _this.message(true, data.data.msg, 'success')
            _this.dialogVisible = false
        } else {
            _this.message(true, data.data.msg, 'error')
        }
    }
    ,
    /**
     * ifshow: true/false msg: message  type: error/warning/success
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
        this.getFormData1()
    }
    }
</script>
<style>

    .avatar-uploader .el-upload {
        border: 1px dashed #d900d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }

    .avatar-uploader .el-upload:hover {
        border-color: #400000;
    }

    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c009d;
        width: 178px;
        height: 78px;
        line-height: 78px;
        text-align: center;
    }

    .avatar {
        width: 180px;
        height: 0px;
        display: table;
    }
</style>