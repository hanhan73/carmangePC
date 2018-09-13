<template>
    <div class="">
        <div>
            <div class="m-b-5 ovf-hd fz-14 search-bar">
                <!-- <h2>一键救援</h2> -->
                <el-input
                size="mini"
                class="w-200"
                placeholder="请输入关键词搜索"
                v-model="input10"
                clearable>
                </el-input>
                <div class="btn-wrapper inline">
                    <el-button size="mini" type="primary" icon="search">搜索</el-button>
                </div>
            </div>
            <el-table :data="oneBtuHelp" style="width: 100%" size="mini" stripe>
                <el-table-column
                prop="ordernum"
                label="订单编号"
                width="120">
                </el-table-column>
                <el-table-column
                prop="name"
                label="车主姓名"
                width="120">
                </el-table-column>
                <el-table-column
                prop="phone"
                width="120"
                label="车主手机">
                </el-table-column>
                <el-table-column
                prop="carnum"
                width="120"
                label="救援车牌">
                </el-table-column>
                <el-table-column
                prop="cartype"
                width="120"
                label="车型">
                </el-table-column>
                <el-table-column
                prop="service"
                width="120"
                label="服务项目">
                </el-table-column>
                <el-table-column
                prop="address"
                width="180"
                label="救援地点">
                </el-table-column>
                <el-table-column
                prop="serviceplan"
                width="120"
                label="服务方案">
                </el-table-column>
                <el-table-column
                prop="helpstore"
                width="140"
                label="救援门店">
                </el-table-column>
                <el-table-column
                prop="phonestore"
                width="180"
                label="地址">
                </el-table-column>
                <el-table-column
                prop="helpstate"
                width="100"
                label="救援状态">
                  <template slot-scope="{ row: { helpstate }}">
                    <a v-bind:style="{ color: activeColor1 }" href="#" v-if="helpstate === 1">已完成</a>
                    <a v-bind:style="{ color: activeColor2 }" href="#" v-if="helpstate === 0">未分派</a>
                    <a v-bind:style="{ color: activeColor3 }" href="#" v-if="helpstate === 3">已分派</a>
                  </template>
                </el-table-column>
                <el-table-column
                min-width="120px"
                label="操作">
                    <template slot-scope="{ row: { serviceplan, carnum, helpstore, helpstate} }">
                        <el-button-group>
                            <el-button type="primary" size="mini" @click="$refs.edit.open(id)">服务选择</el-button>
                            <el-button type="danger" size="mini" @click="$refs.sendinfo.open(carnum)">发消息</el-button>
                            <el-button type="primary" size="mini" @click="$refs.sendinfo.open(carnum)">服务进程</el-button>
                        </el-button-group>
                    </template>
                </el-table-column>
            </el-table>
             <div class="pos-rel p-t-10 pages-footer">
                <div class="stat">
                </div>
                <div class="block pages">
                    <el-pagination
                    @current-change="getRechargeInfo"
                    :current-page.sync="listQuery.currentPage"
                    :page-size="listQuery.pageSize"
                    :page-sizes="[15, 30, 60, 100]"
                    layout="total, prev, pager, next, jumper"
                    :total="counts2">
                    </el-pagination>
                </div>
             </div>
        </div>
        <sendinfo ref="sendinfo"></sendinfo>
    </div>
</template>

<script>
import Sendinfo from './send-info';

export default {
  data() {
    return {
      activeColor1: '#409EFF',
      activeColor2: '#F56C6C',
      activeColor3: '#E6A23C',
      oneBtuHelp: [
        {
          ordernum: '124565',
          name: '辅导费',
          phone: '1234789794',
          carnum: '445454545',
          cartype: 'dad',
          service: 'fafda',
          address: 'dadfddfdfdfdfd',
          serviceplan: 'daddada',
          helpstore: '深圳西丽大学城店',
          phonestore: '深圳南山西丽',
          helpstate: 0,
        },
      ],
      listQuery: {
        pageSize: 10,
        fatherid: null,
        currentPage: 1,
        loginId: Lockr.get('securId'),
      },
      listQuery2: {
        pageSize: 10,
        fatherid: null,
        currentPage: 1,
        loginId: Lockr.get('securId'),
      },
    };
  },
  components: {
    // Add,
    Sendinfo,
  },
  methods: {
    helpstate() {
    },
  },
};
</script>


<style lang="stylus" scoped>
h2 {
    margin-top: 5px;
    margin-bottom: 5px;
    margin-left: 5px;
    margin-right: 5px;
    padding-left: 10px;
    padding-top: 5px;
    padding-bottom: 5px;
    border-left: 4px solid #409EFF;
    background: #fff;
    // &.bottom
}
</style>



