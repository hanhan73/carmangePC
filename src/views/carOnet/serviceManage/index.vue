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
          <el-button size="mini" type="primary" icon="el-icon-search">搜索</el-button>
          <el-button size="mini" type="primary" icon="search">分配权限</el-button>
          <el-button size="mini" type="primary" icon="el-icon-download">批量导入</el-button>
      </div>
    </div>
     <el-table :data="serviceList" height="350px" style="width: 100%" size="mini" stripe>
        <el-table-column
        prop="servicename"
        label="服务商名称"
        width="180">
        </el-table-column>
        <el-table-column
        prop="person"
        label="负责人"
        width="180">
        </el-table-column>
        <el-table-column
        prop="phone"
        label="联系电话"
        width="180">
        </el-table-column>
        <el-table-column
        prop="serviceAddress"
        label="服务商地址"
        width="200">
        </el-table-column>
        <el-table-column
        prop="serviceCount"
        label="服务接单数"
        width="140">
        </el-table-column>
        <el-table-column
        prop="loginAccount"
        label="登录账号"
        width="120">
        </el-table-column>
        <el-table-column
        prop="loginPwd"
        label="登录密码"
        width="120">
        </el-table-column>
        <el-table-column
        prop="accountRights"
        label="账号权限"
        width="120">
        </el-table-column>
        <el-table-column
        prop="remark"
        label="备注"
        width="120">
        </el-table-column>
        <el-table-column
        min-width="100px"
        label="操作">
            <template slot-scope="{ row: { serviceplan, carnum, helpstore, helpstate} }">
                <el-button-group>
                    <el-button type="warning" size="mini" @click="$refs.sendinfo.open(carnum)">分配权限</el-button>
                    <el-button type="primary" size="mini" @click="$refs.edit.open(id)">编辑</el-button>
                </el-button-group>
            </template>
        </el-table-column>
     </el-table>
     
  </div>
</template>
