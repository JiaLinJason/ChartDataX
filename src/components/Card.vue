<template>
    <div style="padding: 10px;">
        <div class="card">
            <el-card
                v-if="isButton"
                :body-style="{ padding: '0px' }"
                shadow="hover">
                <div class="wrapper" @click="handleCardClick">
                    <i class="el-icon-plus" style="font-size: 34px;line-height: 265px;"></i>
                </div>
            </el-card>
            <el-card
                v-else
                :body-style="{ padding: '0px' }"
                shadow="hover">
                <div class="wrapper">
                    <img src="../assets/card.png" @click="handleCardClick">
                    <div class="operator">
                        <div class="title text-ellipsis">{{chartName}}</div>
                        <el-popover
                            placement="top"
                            v-model="visible">
                        <p>确定删除此表格吗？</p>
                        <div style="text-align: right; margin: 0">
                            <el-button size="mini" type="text" @click="visible = false">取消</el-button>
                            <el-button type="primary" size="mini" @click="deleteClick">确定</el-button>
                        </div>
                        <template #reference>
                            <i class="el-icon-delete icon-btn" style="color: #F56C6C;"></i>
                        </template>
                        </el-popover>
                        <i class="el-icon-edit icon-btn"></i>
                    </div>
                </div>
            </el-card>
        </div>
    </div>
</template>

<script>
export default {
    name: "Card",
    props: {
        chartName: {
            type: String
        },
        chartId: {
            type: Number
        },
        isButton: {
            require: true,
            type: Boolean
        }
    },
    data() {
        return {
            visible: false,
        }
    },
    methods: {
        deleteClick() {
            this.visible = false;
            this.$emit('cardDelete', this.chartId);
        },
        handleCardClick() {
            this.$emit('cardClick', this.isButton, this.chartId);
        }
    }
};
</script>
<style scoped>
.wrapper {
    display: inline-block;
    width: 100%;
    height: 265px;
    font-size: 0px;
    text-align: center;
    color: #aaa;
}
.wrapper img {
    width: 100%;
    cursor: pointer;
    user-select: none;
    height: 200px;
}
.text-ellipsis {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}
.operator {
    padding: 10px;
    font-size: 14px;
    text-align: left;
    box-sizing: border-box;
    height: 65px;
    color: #2c3e50;
}
.title {
    padding-left: 10px;
    box-sizing: border-box;
    width: 100%;
    height: 20px;
}
.icon-btn {
    cursor: pointer;
    font-size: 18px;
    float: right;
    padding: 4px 12px 4px 0px;
}
.icon-btn:hover {
    color: #3f9eff;
}
</style>
