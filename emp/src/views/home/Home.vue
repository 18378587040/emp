<template>
    <div>
<!--        <el-row :gutter="30" style="display: flex; justify-content: center;">-->
<!--            <el-col :span="26">-->
                <el-card style="height: 70px;background-color: #D2E9FF">
                    <div style="font-size: 27px;font-family: '微软雅黑';text-align: center;">
                        欢迎使用员工事务管理系统
                    </div>
                </el-card>
<!--            </el-col>-->
<!--        </el-row>-->
        <!-- 统计数据 -->
        <el-row :gutter="30" style="margin-top:1.25rem">
            <el-col :span="6">
                <el-card>
                    <div class="card-icon-container">
                        <i class="el-icon-s-check" style="color:#40C9C6" />
                    </div>
                    <div class="card-desc">
                        <div class="card-title">员工</div>
                        <div class="card-count">{{ empInfo.homeEmployees }}</div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card>
                    <div class="card-icon-container">
                        <i class="el-icon-c-scale-to-original" style="color:#34BFA3" />
                    </div>
                    <div class="card-desc">
                        <div class="card-title">总部门</div>
                        <div class="card-count">{{ empInfo.homeDeparts }}</div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card>
                    <div class="card-icon-container">
                        <i class="el-icon-date" style="color:#F4516C" />
                    </div>
                    <div class="card-desc">
                        <div class="card-title">已请假</div>
                        <div class="card-count">{{ empInfo.homeLeaves }}</div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card>
                    <div class="card-icon-container">
                        <i class="el-icon-chat-dot-round" style="color:#36A3F7" />
                    </div>
                    <div class="card-desc">
                        <div class="card-title">通知信息</div>
                        <div class="card-count">{{ empInfo.homeNotices }}</div>
                    </div>
                </el-card>
            </el-col>
        </el-row>
        <el-row :gutter="30" style="margin-top:1.25rem">
            <el-col :span="8">
                <el-card>
                    <div class="e-title" style="display: flex;justify-content: center">通知</div>
                    <div style="height:350px">
                        <div  v-for="notice in notices" :key="notice.id" class="text item">
                            <table>
                                <tr>
                                    <td>发布人：</td>
                                    <td>{{notice.name}}</td>
                                </tr>
                                <tr>
                                    <td>发布内容：</td>
                                    <td>{{notice.content}}</td>
                                </tr>
                                <tr>
                                    <td>发布时间：</td>
                                    <td>{{notice.createTime | date}}</td>
                                </tr>
                            </table>
                        </div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="16">
                <el-card>
                    <div class="e-title" style="display: flex;justify-content: center">员工职位统计</div>
                    <div style="height:350px">
                        <v-chart :options="departDistribution" v-loading="loading" />
                    </div>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    export default {
        created() {
            this.getEmpInfo();
            this.getData();
            this.getHomePosition();
        },
        data: function() {
            return {
                homePosition: [],
                notices: [],
                loading: true,
                empInfo: {
                    homeEmployees: 0,
                    homeDeparts: 0,
                    homeLeaves: 0,
                    homeNotices: 0,
                },
                departDistribution :{
                    backgroundColor: "transparent",
                    tooltip: {
                        trigger: "axis",
                        axisPointer: {
                            type: "shadow",
                        },
                    },
                    grid: {
                        top: "18%",
                        right: "2%",
                        left: "10%",
                        bottom: "15%",
                    },
                    xAxis: [
                        {
                            type: "category",
                            data: [],
                        },
                    ],
                    yAxis: [
                        {
                            name: "单位:%",
                            nameTextStyle: {
                                color: " rgba(255, 255, 255, 0.6)",
                                fontSize: 12,
                                padding: 12,
                            },
                            axisLine: {
                                show: false,
                            },
                            splitLine: {
                                lineStyle: {
                                    type: "dashed",
                                    color: "rgba(112,112,112,0.5)",
                                },
                            },
                        },
                    ],
                    series: [
                        {
                            data: [],
                            type: "bar",
                            barWidth: 14,
                            itemStyle: {
                                color: {
                                    type: "linear",
                                    x: 0,
                                    y: 0,
                                    x2: 1,
                                    y2: 1,
                                    colorStops: [
                                        {
                                            offset: 0,
                                            color: "rgba(64, 200, 169, 0.8)", // 0% 处的颜色
                                        },
                                        {
                                            offset: 1,
                                            color: "rgba(64, 200, 169, 0)", // 100% 处的颜色
                                        },
                                    ],
                                },
                                borderColor: "#40C8A9",
                                borderType: "solid",
                                barBorderRadius: [15, 15, 0, 0],
                            },
                        },
                    ],
                }
            };
        },
        methods: {
            getEmpInfo() {
                this.axios.get("/api/home/info").then((data) => {
                    if (data) {
                        this.empInfo = data;
                    }
                });
            },
            getData() {
                this.axios.get("/api/home/notice").then((data) => {
                    if (data) {
                        this.notices = data;
                    }
                    this.loading = false;
                });
            },
            getHomePosition() {
                this.axios.get("/api/home/position").then((data) => {
                    if (data) {
                        data.forEach(item => {
                            this.departDistribution.series[0].data.push(item.count);
                            this.departDistribution.xAxis[0].data.push(item.name);
                        });
                        this.loading = false;
                    }
                });
            },
        }
    };
</script>

<style scoped>
    .card-icon-container {
        display: inline-block;
        font-size: 3rem;
    }
    .card-desc {
        font-weight: bold;
        float: right;
    }
    .card-title {
        margin-top: 0.3rem;
        line-height: 18px;
        color: rgba(0, 0, 0, 0.45);
        font-size: 1rem;
    }
    .card-count {
        margin-top: 0.75rem;
        color: #666;
        font-size: 1.25rem;
    }
    .echarts {
        width: 100%;
        height: 100%;
    }
    .e-title {
        font-size: 13px;
        color: #202a34;
        font-weight: 700;
    }
    .text {
        font-size: 14px;
    }

    .item {
        padding: 18px 0;
    }

    .box-card {
        width: 480px;
    }
</style>
