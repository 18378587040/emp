<template>
    <el-card class="main-card">
        <div class="title">{{ this.$route.name }}</div>
        <!-- 表格操作 -->
        <div class="operation-container">
            <el-button
                    type="primary"
                    size="small"
                    icon="el-icon-plus"
                    @click="openModel(null)"
            >
                新增
            </el-button>
            <el-button
                    type="danger"
                    size="small"
                    icon="el-icon-delete"
                    :disabled="noticeIdList.length == 0"
                    @click="deleteFlag = true"
            >
                批量删除
            </el-button>
            <!-- 条件筛选 -->
            <div style="margin-left:auto">
                <el-input
                        v-model="keywords"
                        prefix-icon="el-icon-search"
                        size="small"
                        placeholder="请输入发布人"
                        style="width:200px"
                        @keyup.enter.native="searchnotices"
                />
                <el-button
                        type="primary"
                        size="small"
                        icon="el-icon-search"
                        style="margin-left:1rem"
                        @click="searchnotices"
                >
                    搜索
                </el-button>
            </div>
        </div>
        <!-- 表格展示 -->
        <el-table
                size="mini"
                border
                :data="noticeList"
                @selection-change="selectionChange"
                v-loading="loading"
        >
            <!-- 表格列 -->
            <el-table-column type="selection" width="55" />
            <el-table-column prop="id" label="编号" align="center" />
            <el-table-column prop="name" label="发布人" align="center" />
            <el-table-column prop="content" label="发布内容" align="center" />
            <el-table-column
                    prop="createTime"
                    label="创建时间"
                    width="140"
                    align="center"
            >
                <template slot-scope="scope">
                    <i class="el-icon-time" style="margin-right:5px" />
                    {{ scope.row.createTime | date }}
                </template>
            </el-table-column>
            <!-- 列操作 -->
            <el-table-column label="操作" align="center" width="160">
                <template slot-scope="scope">
                    <el-button type="primary" size="mini" @click="openModel(scope.row)">
                        编辑
                    </el-button>
                    <el-popconfirm
                            title="确定删除吗？"
                            style="margin-left:1rem"
                            @confirm="deletenotice(scope.row.id)"
                    >
                        <el-button size="mini" type="danger" slot="reference">
                            删除
                        </el-button>
                    </el-popconfirm>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-pagination
                class="pagination-container"
                background
                @size-change="sizeChange"
                @current-change="currentChange"
                :current-page="current"
                :page-size="size"
                :total="count"
                :page-sizes="[10, 20]"
                layout="total, sizes, prev, pager, next, jumper"
        />
        <!-- 批量删除对话框 -->
        <el-dialog :visible.sync="deleteFlag" width="30%">
            <div class="dialog-title-container" slot="title">
                <i class="el-icon-warning" style="color:#ff9900" />提示
            </div>
            <div style="font-size:1rem">是否删除选中项？</div>
            <div slot="footer">
                <el-button @click="deleteFlag = false">取 消</el-button>
                <el-button type="primary" @click="deletenotice(null)">
                    确 定
                </el-button>
            </div>
        </el-dialog>
        <!-- 添加对话框 -->
        <el-dialog :visible.sync="addOrEdit" width="30%">
            <div class="dialog-title-container" slot="title" ref="noticeTitle" />
            <el-form label-width="80px" size="medium" :model="noticeForm">
                <el-form-item label="发起人">
                    <el-input style="width:250px" v-model="noticeForm.name" />
                </el-form-item>
                <el-form-item label="发布内容">
                    <el-input style="width:250px" v-model="noticeForm.content" />
                </el-form-item>
            </el-form>
            <div slot="footer">
                <el-button @click="addOrEdit = false">取 消</el-button>
                <el-button type="primary" @click="addOrEditCategory">
                    确 定
                </el-button>
            </div>
        </el-dialog>
    </el-card>
</template>

<script>
    export default {
        created() {
            this.listnotices();
        },
        data: function() {
            return {
                loading: true,
                deleteFlag: false,
                addOrEdit: false,
                noticeIdList: [],
                noticeList: [],
                noticeForm: {
                    name: "admin",
                    content: "",
                },
                keywords: null,
                current: 1,
                size: 10,
                count: 0
            };
        },
        methods: {
            selectionChange(noticeList) {
                this.noticeIdList = [];
                noticeList.forEach(item => {
                    this.noticeIdList.push(item.id);
                });
            },
            searchnotices() {
                this.current = 1;
                this.listnotices();
            },
            sizeChange(size) {
                this.size = size;
                this.listnotices();
            },
            currentChange(current) {
                this.current = current;
                this.listnotices();
            },
            deletenotice(id) {
                var param = {};
                if (id == null) {
                    param = { data: this.noticeIdList };
                } else {
                    param = { data: [id] };
                }
                this.axios.delete("/api/admin/notice", param).then(( data ) => {
                    if (data) {
                        this.$notify.success({
                            title: "成功",
                            message: "操作成功"
                        });
                        this.listnotices();
                    } else {
                        this.$notify.error({
                            title: "失败",
                            message: "操作失败"
                        });
                    }
                    this.deleteFlag = false;
                });
            },
            openModel(notice) {
                if (notice != null) {
                    console.log(notice)
                    this.noticeForm = JSON.parse(JSON.stringify(notice));
                    this.$refs.noticeTitle.innerHTML = "修改通知";
                } else {
                    this.noticeForm.name = "admin";
                    this.noticeForm.content = "";
                    this.$refs.noticeTitle.innerHTML = "发布通知";
                }
                this.addOrEdit = true;
            },
            addOrEditCategory() {
                // if (this.noticeForm.content.trim() == "") {
                //     this.$message.error("通知内容不能为空");
                //     return false;
                // }
                this.axios.post("/api/admin/notice", this.noticeForm).then((data) => {
                    if (data) {
                        this.$notify.success({
                            title: "成功",
                            message: "操作成功"
                        });
                        this.listnotices();
                    } else {
                        this.$notify.error({
                            title: "失败",
                            message: "操作失败"
                        });
                    }
                    this.addOrEdit = false;
                });
            },
            listnotices() {
                this.axios
                    .get("/api/admin/notice", {
                        params: {
                            current: this.current,
                            size: this.size,
                            keywords: this.keywords
                        }
                    })
                    .then((data) => {
                        this.noticeList = data.recordList;
                        this.count = data.count;
                        this.loading = false;
                    });
            }
        }
    };
</script>
