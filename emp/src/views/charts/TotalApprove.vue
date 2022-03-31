<template>
    <el-card class="main-card">
        <div class="title">{{ this.$route.name }}</div>
        <div class="operation-container">
        </div>
        <el-row>
            <el-col :span="24">
                <el-card>
                    <div class="e-title" style="display: flex;justify-content: center">申请总览</div>
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
        created() {
            this.getHomeApprove();
        },
        data: function () {
            return {
                loading: true,
                departDistribution :{
                    tooltip: {
                        trigger: 'item'
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left'
                    },
                    series: [
                        {
                            type: 'pie',
                            radius: '50%',
                            data: [],
                            emphasis: {
                                itemStyle: {
                                    shadowBlur: 10,
                                    shadowOffsetX: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                }
                            }
                        }
                    ]
                },
                getHomeApprove() {
                    this.axios.get("/api/home/approve").then((data) => {
                        if (data) {
                            data.forEach(item => {
                                this.departDistribution.series[0].data.push(item);
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