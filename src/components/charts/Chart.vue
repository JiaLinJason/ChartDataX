<template>
    <div id="chartDom" class="chart">
    </div>
</template>
<script>
import * as echarts from "echarts";
export default {
    name: 'Chart',
    props: {
        chartData: {
            type: Object
        }
    },
    data() {
        return {
            chartInstance: null
        }
    },
    watch: {
        chartData() {
            this.chartInstance.setOption && this.chartInstance.setOption(this.chartData);
        }
    },
    beforeDestroy() {
        this.destroyInstance();
        window.onresize = null;
    },
    mounted() {
        this.initChart();
    },
    methods: {
        initChart() {
            if (this.chartInstance !== null) {
                this.destroyInstance();
            }
            let dom = document.getElementById("chartDom");
            let chartInstance = echarts.init(dom);
            window.onresize = function () {
                chartInstance.resize();
            }
            this.chartInstance = chartInstance;
            chartInstance.setOption(this.chartData);
        },
        destroyInstance() {
            this.chartInstance.dispose && this.chartInstance.dispose();
        }
    }
}
</script>

<style scoped>
.chart {
    height: 400px;
}
</style>