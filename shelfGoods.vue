<template>
    <div class="main-border">
        <el-menu default-active="1" class="el-menu-demo" mode="horizontal" @select="handleSelect">
            <span class="app-title">
                <el-input placeholder="商品名称..." v-model="findValue" @keyup.enter.native="searchIdle">
                    <el-button slot="append" icon="el-icon-search" @click="searchIdle"></el-button>
                </el-input>
            </span>
            <el-menu-item @click="status = 1" index="1">上架商品</el-menu-item>
        </el-menu>
        <el-button type="primary" @click="openAddGoodsDialog">+添加商品</el-button>

        <el-table v-show="this.mode == 1"
                  :data="OfflineGoods"
                  stripe
                  style="width: 100%;color: #5a5c61;">
            <el-table-column
                    prop="releaseTime"
                    label="发布日期"
                    width="200">
            </el-table-column>
            <el-table-column
                    prop="idleName"
                    label="商品名称"
                    show-overflow-tooltip
            >
            </el-table-column>
            <el-table-column
                    prop="user.nickname"
                    label="发布用户"
                    show-overflow-tooltip
                    min-width="100"
                    width="100">
            </el-table-column>
            <el-table-column
                    prop="idlePrice"
                    label="价格"
                    show-overflow-tooltip
                    min-width="100"
                    width="100">
            </el-table-column>
            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button
                            size="mini"
                            type="danger"
                            @click="deleteGoods(scope.$index)">删除
                    </el-button>
                    <el-button
                            size="mini"
                            @click="toRelease(scope.$index)">发布商品
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
        <div class="block">
            <el-pagination
                    @current-change="handleCurrentChange"
                    :current-page.sync="nowPage"
                    :page-size="8"
                    background
                    layout="prev, pager, next,jumper"
                    :total="total">
            </el-pagination>
        </div>

        <el-dialog :visible.sync="addGoodsDialogVisible" title="添加商品">
            <release></release>
        </el-dialog>
    </div>
</template>

<script>
import Release from '../page/release.vue';
import options from '../common/country-data.js';

export default {
    name: "IdleGoods",
    components: {
        Release
    },
    data() {
        return {
            mode: 1,
            nowPage: 1,
            total: 0,
            onlineGoods: [],
            OfflineGoods: [],
            findValue: '',
            status: 1,
            addGoodsDialogVisible: false
        };
    },
    created() {
        this.getOnlineGoods();
    },
    methods: {
        searchIdle() {
            this.$api.queryIdle({
                findValue: this.findValue,
                page: this.nowPage,
                nums: 8,
                status: this.status
            }).then(res => {
                console.log(res);
                if (res.status_code == 1 && res.data.list != null) {
                    if (res.data.list[0].idleStatus == 1) {
                        this.onlineGoods = res.data.list;
                        this.total = res.data.count;
                    } else {
                        this.OfflineGoods = res.data.list;
                        this.total = res.data.count;
                    }
                } else {
                    this.$message.error(res.msg);
                }
            }).catch(e => {
                console.log(e);
            });
        },
        handleCurrentChange(val) {
            this.nowPage = val;
            if (this.mode == 1) {
                this.getOnlineGoods();
            }
            if (this.mode == 2) {
                this.getOfflineGoods();
            }
        },
        handleSelect(val) {
            if (this.mode !== val) {
                this.mode = val;
                if (val == 1) {
                    this.nowPage = 1;
                    this.getOnlineGoods();
                }
                if (val == 2) {
                    this.nowPage = 1;
                    this.getOfflineGoods();
                }
                if (val == 3) {
                    this.addGoodsDialogVisible = true;
                }
            }
        },
        makeOfflineGoods(i) {
            this.$api.updateGoods({
                id: this.onlineGoods[i].id,
                status: 2
            }).then(res => {
                if (res.status_code == 1) {
                    this.getOnlineGoods();
                } else {
                    this.$message.error(res.msg);
                }
            }).catch(e => {
                console.log(e);
            });
        },
        deleteGoods(i) {
            this.$api.updateGoods({
                id: this.OfflineGoods[i].id,
                status: 0
            }).then(res => {
                if (res.status_code == 1) {
                    this.getOfflineGoods();
                } else {
                    this.$message.error(res.msg);
                }
            }).catch(e => {
                console.log(e);
            });
        },
        getOnlineGoods() {
            this.$api.getGoods({
                status: 1,
                page: this.nowPage,
                nums: 8
            }).then(res => {
                if (res.status_code == 1) {
                    this.onlineGoods = res.data.list;
                    this.total = res.data.count;
                } else {
                    this.$message.error(res.msg);
                }
            }).catch(e => {
                console.log(e);
            });
        },
        getOfflineGoods() {
            this.$api.getGoods({
                status: 2,
                page: this.nowPage,
                nums: 8
            }).then(res => {
                if (res.status_code == 1) {
                    this.OfflineGoods = res.data.list;
                    this.total = res.data.count;
                } else {
                    this.$message.error(res.msg);
                }
            }).catch(e => {
                console.log(e);
            });
        },
        openAddGoodsDialog() {
            this.addGoodsDialogVisible = true;
        }
    }
};
</script>

<style scoped>
.main-border {
    background-color: #FFF;
    padding: 10px 30px;
    box-shadow: 0 1px 15px -6px rgba(0, 0, 0, .5);
    border-radius: 5px;
}

.block {
    display: flex;
    justify-content: center;
    padding-top: 15px;
    padding-bottom: 10px;
    width: 100%;
}
</style>