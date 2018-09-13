<template>
  <div class="">
    <div class="m-b-5 ovf-hd fz-14 search-bar">
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
    <el-table :data="customlist"  style="width: 100%" size="mini" stripe>
        <el-table-column
        prop="carownerName"
        label="车主姓名"
        width="180">
        </el-table-column>
        <el-table-column
        prop="carownerPhone"
        label="车主手机"
        width="120">
        </el-table-column>
        <el-table-column
        prop="carNum"
        label="车牌"
        width="120">
        </el-table-column>
        <el-table-column
        prop="carType"
        label="车型"
        width="120">
        </el-table-column>
        <el-table-column
        prop="latelyhelpTime"
        label="最近求助时间"
        width="180">
        </el-table-column>
        <el-table-column
        prop="keepfitTime"
        label="最近保养时间"
        width="180">
        </el-table-column>
        <el-table-column
        prop="latelyPackage"
        label="最近套餐"
        width="180">
        </el-table-column>
        <el-table-column
        prop="servicePerson"
        label="服务人员"
        width="100">
        </el-table-column>
        <el-table-column
        prop="serviceTime"
        label="服务时间"
        width="180">
        </el-table-column>
        <el-table-column
        label="操作">
          <template slot-scope="scope">
            <el-button-group>
                <el-button type="primary" size="mini" @click="$refs.serviceinfo.open()">服务记录</el-button>
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
    <serviceinfo ref="serviceinfo"></serviceinfo>
  </div>
</template>

<script>
import Serviceinfo from './service-info';

export default {
  data() {
    return {
      customlist: [
        {
          carownerName: 'dadada',
          carownerPhone: '1354678924',
          carNum: 'Gh4561',
          carType: 'kk',
          latelyhelpTime: '2018-10-10 05:56:03',
          keepfitTime: '2018-05-06 12:03:02',
          latelyPackage: 'kk',
          servicePerson: 'cc',
          serviceTime: '2018-02-03 06:20:20',
        },
      ],
      listQuery: {
        pageSize: 10,
        fatherid: null,
        currentPage: 1,
        loginId: Lockr.get('securId'),
      },
    };
  },
  components: {
    Serviceinfo,
  },
};
</script>

