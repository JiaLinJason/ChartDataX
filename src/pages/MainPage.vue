<template>
    <main-layout>
        <el-row>
            <el-col v-for="item in chartList" :key="item.id" :span="4">
                <card
                    :chartId="item.id"
                    :chartName="item.title"
                    :isButton="item.id === 9999"
                    @cardClick="cardClick"
                    @cardDelete="cardDelete"
                ></card>
            </el-col>
        </el-row>
        <el-dialog
            title="创建项目"
            destroy-on-close
            :before-close="clearAddDialog"
            :visible.sync="showAddDialog"
            width="30%">
        <div>
            项目名称:
            <el-input v-model="objectName" placeholder="请输入项目名称"></el-input>
            <el-divider></el-divider>
            <el-upload
            class="upload-demo"
            ref="excel-upload"
            action="https://jsonplaceholder.typicode.com/posts/"
            accept=".xls,.xlsx"
            :limit="1"
            :on-change="chartUpload"
            :auto-upload="false">
                <el-button size="small" type="primary">点击上传</el-button>
                <div slot="tip" class="el-upload__tip">只能上传xls/xlsx文件，且不超过500kb</div>
            </el-upload>
        </div>
        <span slot="footer" class="dialog-footer">
            <el-button @click="showAddDialog = false">取 消</el-button>
            <el-button type="primary" @click="addChart">确 定</el-button>
        </span>
        </el-dialog>
        <el-dialog
            destroy-on-close
            :close-on-click-modal="false"
            :before-close="clearDetailDialog"
            :title="currentProjectName"
            :visible.sync="showDetailDialog"
            width="70%">
        <div>
            <chart
            v-if="showChart"
            :chartData="currentChartData"
            ></chart>
        </div>
        <span slot="footer" class="dialog-footer">
            <el-button @click="showDetailDialog = false">取 消</el-button>
            <el-button type="primary" @click="showDetailDialog = false">确 定</el-button>
        </span>
        </el-dialog>
    </main-layout>
</template>

<script>
import MainPageLayout from '../components/layout/MainPageLayout';
import baseUtil from '../utils/baseUtil';
import XLSX from 'xlsx';
import Card from '../components/Card';
import Chart from '../components/charts/Chart';
export default {
    name: 'MainPage',
    data () {
        return {
            showAddDialog: false,
            showDetailDialog: false,
            showChart: false,
            objectName: '',
            currentUploadData: {},
            list: [],
            currentProjectName: '',
            currentChartData: {}
        };
    },
    computed: {
        chartList() {
            return [...this.list, { id: 9999, title: '' }];
        }
    },
    methods: {
        cardClick(isAdd, chartId) {
            if (isAdd) {
                this.showAddDialog = true;
            } else {
                let list = baseUtil.simpleClone(this.list);
                let target = list.find(item => item.id === chartId);
                const { data } = target;
                let legend = data.series.map(item => item.name);
                this.currentProjectName = target.title;
                this.currentChartData = {
                    tooltip: {
                        trigger: 'axis'
                    },
                    legend: {data: legend},
                    xAxis: {
                        type: 'category',
                        boundaryGap: false,
                        data: data.xAxisData
                    },
                     yAxis: {
                        type: 'value'
                    },
                    series: data.series
                }
                this.showChart = true;
                this.$nextTick(() => {
                    this.showDetailDialog = true;
                });
            }
        },
        clearDetailDialog(done) {
            this.currentChartData = {};
            this.currentProjectName = '';
            this.showChart = false;
            done();
        },
        clearAddDialog(done) {
            this.objectName = '';
            done();
        },
        addChart() {
            let objectName = this.objectName;
            let chartData = baseUtil.simpleClone(this.currentUploadData);
            if (JSON.stringify(chartData) !== '{}') {
                this.objectName = '';
                this.currentUploadData = {};
                let list = baseUtil.simpleClone(this.list);
                list.push({
                    id: list.length + 1,
                    title: objectName || '项目' + (Number(list.length) + 1),
                    data: chartData
                })
                this.list = list;
                this.showAddDialog = false;
            } else {
                this.$message.error('必须上传一个文件以创建图表项目');
            }
        },
        chartUpload(file, fileList) {
            let files = {0:file.raw}
            this.readExcel(files).then((data) => {
                Array.isArray(data) && (function () {
                    let xAxisData = [];
                    let series = [];
                    for (let key in data[0]) {
                        if (key !== '__EMPTY') {
                            xAxisData.push(key);
                        }
                    }
                    series = data.map(s => {
                        let line = [];
                        for (let key in s) {
                            if (key !== '__EMPTY') {
                                line.push(s[key]);
                            }
                        }
                        return {
                            name: s['__EMPTY'],
                            type: 'line',
                            data: line
                        }
                    });
                    this.currentUploadData = {xAxisData, series};
                }).bind(this)();
            }).catch((error) => {
                this.$message.error(error.message);
            });
        },
        readExcel (files) {
            return new Promise((resolve, reject) => {
                if(files.length <= 0){//如果没有文件名
                    reject(new Error('未选择文件'));
                }else if(!/\.(xls|xlsx)$/.test(files[0].name.toLowerCase())){
                    reject(new Error('上传格式不正确，请上传xls或者xlsx格式'));
                } else {
                    const fileReader = new FileReader();
                    fileReader.onload = (ev) => {
                        try {
                            const data = ev.target.result;
                            const workbook = XLSX.read(data, {
                                type: 'binary'
                            });
                            const wsname = workbook.SheetNames[0];
                            const ws = XLSX.utils.sheet_to_json(workbook.Sheets[wsname]);
                            resolve(ws);
                        } catch (e) {
                            reject(e)
                            return false;
                        }
                    };
                fileReader.readAsBinaryString(files[0]);
                }
            });
        },
        cardDelete(id) {
            let list = baseUtil.simpleClone(this.list);
            let targetIndex = list.findIndex(item => item.id === id);
            if (targetIndex !== -1) {
                list.splice(targetIndex, 1);
                this.$message({
                    message: '删除成功',
                    type: 'success'
                });
            } else {
                this.$message.error('删除失败，原因: ID不存在');
            }
            this.list = list;
        }
    },
    components: {
        'main-layout': MainPageLayout,
        Card,
        Chart
    }
}
</script>

<style scoped>
.el-row {
    padding: 0px 15px;
}
</style>