<template>
    <el-card class="main-card">
        <div class="title">{{ this.$route.name }}</div>
        <div class="operation-container">
        </div>
        <el-row>
            <el-col :span="24">
                <el-card>
                    <div class="e-title" style="display: flex;justify-content: center">员工职位总览</div>
                    <div style="height:450px">
                        <v-chart style="height: 100%;width: 100%" :options="departDistribution" v-loading="loading" />
                    </div>
                </el-card>
            </el-col>
        </el-row>
    </el-card>
</template>

<script>
    export default {
        name: "TotalPosition",
        created() {
            this.getHomePosition();
        },
        data: function () {
            return {
                loading: true,
                departDistribution :{
                    backgroundColor: "transparent",
                    tooltip: {
                        trigger: "axis",
                        axisPointer: {
                            type: "shadow",
                        },
                    },
                    xAxis: [
                        {
                            name: "职位",
                            type: "category",
                            data: [],
                        },
                    ],
                    yAxis: [
                        {
                            name: "人数",
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
                            },
                        },
                    ],
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
        }
    }
</script>

<style scoped>

</style>