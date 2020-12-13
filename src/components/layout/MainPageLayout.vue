<template>
    <el-container>
        <el-header>
            <div class="header">
                <span class="title">
                    ChartData
                    <label class="special-chart">X</label>
                </span>
            </div>
            <!-- <slot name="header"></slot> -->
        </el-header>
        <el-main>
            <div class="main">
                <slot></slot>
            </div>
        </el-main>
        <!-- <el-footer>
        </el-footer> -->
    </el-container>
</template>

<script>
export default {
    name: "MainPageLayout"
}
</script>

<style scoped>
.el-main,
.el-header {
    padding: 0;
}
.main {
    width: 100%;
    height: calc(100vh - 60px);
    padding: 20px 0px;
}
.title {
    float: left;
    line-height: 60px;
    margin-left: 58px;
    font-size: 23px;
}
.title .special-chart {
    margin-left: -6px;
    color: #3f9eff;
}
.header {
    background-color: #fff;
    width: 100%;
    height: 100%;
    box-shadow: 0px 1px 11px 3px #eee;
}
</style>