
<template>
    <el-row>



    </el-row>


</template>

<script>
    import http from '../utils/http'
    export default {
        data() {
            return {
                filters: {
                    keyword1: ''
                },
                attr: {
                    name2: '',
                    sex2: '',
                    age2: '',
                    phone2: '',
                    clazzId2: '',
                    majorId2: '',
                    instituteId: ''
                },
                institutes:[],    //学院列表
                majors:[],       //专业列表
                clazzs:[],       //班级列表
                sexs:[],          //性别
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
        let data = await http.get('student/list', params)

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
    // 显示添加用户窗口
    showDialogForm() {
        this.dialogFormVisible1 = true
        this.getInstituteData()
        this.getSexData()
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
        } else {
            _this.message(true,data.data.msg,'error')
            _this.clazzs = []
        }
    },
    // 查询性别列表
    async getSexData () {
        let _this = this
        let param = {
            dictTypeCode: 'SEX'
        }
        let data = await http.get('dict/findListByDictTypeCode',param)
        if(!data.data) {
            return
        }
        if (data.data.status === 200) {
            _this.sexs = data.data.data
        } else {
            _this.message(true,data.data.msg,'error')
            _this.sexs = []
        }
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
    // 重置
    resetForm(formName) {
        this.$refs[formName].resetFields();
    },
    // 新增属性
    async addInstitute() {
        let _this = this
        let params = {
            name: _this.attr.name2,
            sex: _this.attr.sex2,
            age: _this.attr.age2,
            phone: _this.attr.phone2,
            clazzId: _this.attr.clazzId2,
            majorId: _this.attr.majorId2,
            instituteId: _this.attr.instituteId2
        }
        let data = await http.post("student/add", params)

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
    // 删除属性提示
    async delAttr () {
        if (this.attrIds.length === 0) {
            this.message(true,'请选择需要删除的属性','warning')
            return
        }
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        }).then(() => {
            this.delAttrs()
    }).catch(() => {
            this.message(true,'已取消删除','warning')
    })
    },
    // 删除属性
    async delAttrs() {
        let _this = this
        let params = {
            ids: _this.attrIds
        }
        let data =await http.post('student/delete', params)

        if(!data.data) {
            return
        }

        if (data.data.status === 200) {
            _this.message(true,data.data.msg,'success')
        } else {
            _this.message(true,data.data.msg,'error')
        }
        _this.getFormData1()
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

    // 属性明细表单提交
    submitForm1(formName) {
        this.$refs[formName].validate((valid) => {
            if (valid) {
                this.addDetail()
            } else {
                console.log('error submit!!');
        return false
    }
    })
    },
    // 添加属性明细
    async addDetail() {
        let _this = this
        let params = {
            id: _this.attrId,
            name: _this.detail.name
        }
        let data = await http.post('SysApi/v1/addAttributeDetail', params)

        if(!data.data) {
            return
        }

        if (data.data.status === 200) {
            _this.resetForm('detail')
            _this.message(true,data.data.msg,'success')
            _this.selDetails(this.attrId)
        } else {
            _this.message(true,data.data.msg,'error')
        }
    },
    // 查询属性明细
    async selDetails(id) {
        let _this = this
        _this.dialogTableVisible1 = true
        _this.attrId = id
        let params = {
            id: id,
            pageSize: _this.pageSize2,
            startPage: _this.startPage2
        }
        let data = await http.get('SysApi/v1/findAttributesDetailByPage', params)

        if(!data.data) {
            return
        }

        if (data.data.status === 200) {
            _this.tableData2 = data.data.data
            _this.total2 = data.data.total
        } else {
            _this.message(true,data.data.msg,'error')
            _this.formData2 = []
        }
    },
    // 删除属性明细
    async delDetails() {
        let _this = this
        if (_this.detailIds.length === 0) {
            this.message(true,'请选择需要删除的属性明细','warning')
            return
        }

        let params = {
            ids: _this.detailIds
        }
        let data =await http.post('SysApi/v1/delAttributeDetails', params)

        if(!data.data) {
            return
        }

        if (data.data.status === 200) {
            _this.message(true,data.data.msg,'success')
        } else {
            _this.message(true,data.data.msg,'error')
        }
        _this.selDetails(this.attrId)
    },
    // 获取选中集
    handleSelectionChange2(val) {
        this.detailIds = []
        if (val) {
            val.forEach(row => {
                this.detailIds.push(row.id)
        });
        }
    },
    // 每页大小改变时触发
    handleSizeChange2 (val) {
        this.pageSize2 = val
        this.selDetails(this.attrId)
    },
    // 当前页码改变时触发
    handleCurrentChange2 (val) {
        this.startPage2 = val
        this.selDetails(this.attrId)
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
    }
    },
    mounted() {

    }
    }
</script>
