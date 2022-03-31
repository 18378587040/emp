<template>
    <el-card class="main-card">
        <div class="title">{{ this.$route.name }}</div>
        <div class="operation-container">
        </div>
        <el-row>
            <el-col :span="24">
                <el-card>
                    <div class="e-title" style="display: flex;justify-content: center">员工部门总览</div>
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
                            name: "部门",
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
                        },
                    ],
                    series: [
                        {
                            data: [],
                            itemStyle: {
                                color: '#D2E9FF',
                            },
                            type: "bar",
                            barWidth: 25,
                        },
                    ],
                },
                getHomePosition() {
                    this.axios.get("/api/home/depart").then((data) => {
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