<template>
  <div>
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
      <el-table :data="oneBtuHelp" style="width: 100%" size="mini" stripe>
          <el-table-column
          prop="ordernum"
          label="订单编号"
          width="180">
          </el-table-column>
          <el-table-column
          prop="name"
          label="车主姓名"
          width="180">
          </el-table-column>
          <el-table-column
          prop="phone"
          label="车主手机">
          </el-table-column>
          <el-table-column
          prop="carnum"
          label="救援车牌">
          </el-table-column>
          <el-table-column
          prop="cartype"
          label="车型">
          </el-table-column>
          <el-table-column
          prop="service"
          label="服务项目">
          </el-table-column>
          <el-table-column
          prop="serviceplan"
          label="服务方案">
          </el-table-column>
          <el-table-column
          prop="helpstore"
          label="服务时间">
          </el-table-column>
          <el-table-column
          prop="phonestore"
          label="地址">
          </el-table-column>
          <el-table-column
          prop="helpstate"
          label="救援状态">
          </el-table-column>
          <el-table-column
          min-width="100px"
          label="操作">
              <template slot-scope="{ row: { serviceplan, carnum, helpstore, helpstate} }">
                  <el-button-group>
                      <el-button type="primary" size="mini" @click="$refs.edit.open(id)">服务选择</el-button>
                      <el-button type="danger" size="mini" @click="$refs.sendinfo.open(carnum)">发消息</el-button>
                  </el-button-group>
              </template>
          </el-table-column>
      </el-table>
  </div>
</template>
