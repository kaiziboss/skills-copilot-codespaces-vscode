<template>
    <div class="main-border">
        <el-menu default-active="1" class="el-menu-demo" mode="horizontal" @select="handleSelect">
            <span class="app-title">
                <el-input placeholder="商品名称..." v-model="findValue" @keyup.enter.native="searchIdle">
                    <el-button slot="append" icon="el-icon-search" @click="searchIdle"></el-button>
                </el-input>
            </span>
            <el-menu-item @click="status=1" index="1">上线的商品</el-menu-item>
            <el-menu-item @click="status =2" index="2">下架的商品</el-menu-item>
            <el-menu-item @click="status =3" index="3">上架商品</el-menu-item>
        </el-menu>

        
        <el-table v-if="this.mode == 1"
                  :data="onlineGoods"
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
                            @click="makeOfflineGoods(scope.$index)">违规下架
                    </el-button>
                    <el-button
                            size="mini"
                            type="danger"
                            @click="edit(scope.$index)">编辑
                    </el-button>
                </template>
            </el-table-column>
        </el-table>

        <el-table v-show="this.mode == 2"
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
                            @click="deleteGoods(scope.$index)">永久删除
                    </el-button>
                    <el-button
                            size="mini"
                            type="danger"
                            @click="edit(scope.$index)">编辑
                    </el-button>
                </template>
            </el-table-column>
        </el-table>

        <el-table v-show="this.mode == 3"
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
    </div>
</template>

<script>
import Release from '../page/release.vue';

export default {
    name: "IdleGoods",
    data() {
        return {
            mode: 1,
            nowPage: 1,
            total: 0,
            onlineGoods: [],
            OfflineGoods: [],
            findValue: '',
            status: 1,
            addGoodsDialogVisible: false,
            newGoods: {
                idleName: '',
                userNickname: '',
                idlePrice: 0,
                releaseTime: ''
            }
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
        addGoods() {
            this.$refs.addGoodsForm.validate((valid) => {
                if (valid) {
                    this.$api.addGoods({
                        idleName: this.newGoods.idleName,
                        userNickname: this.newGoods.userNickname,
                        idlePrice: this.newGoods.idlePrice,
                        releaseTime: this.newGoods.releaseTime,
                        status: 1
                    }).then(res => {
                        if (res.status_code == 1) {
                            this.$message.success('商品上架成功');
                            this.addGoodsDialogVisible = false;
                            this.getOnlineGoods();
                        } else {
                            this.$message.error(res.msg);
                        }
                    }).catch(e => {
                        console.log(e);
                    });
                } else {
                    this.$message.error('请填写完整的商品信息');
                    return false;
                }
            });
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