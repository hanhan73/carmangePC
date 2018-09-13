<template>
  <div class="homePage-wrapper" ref="homePageWrapper">
    <section>
      <el-col :span="20">
        <el-card class="box-card system-info" v-loading="deviceAlertLoading">
          <h1 slot="header">
            今日未读警情
          </h1>
          <article @click="handlePush({path: '/main/alarm/deviceAlert', name: '二押点报警', props: { typeid: '103037,103038,103039' }})">
            <section class="icon"><img width="40px" src="./bet.png"/></section>
            <section class="data">{{twoPointNo}}</section>
            <section class="data">二押</section>
          </article>
          <article @click="handlePush({path: '/main/alarm/deviceAlert', name: '断电报警', props: { typeid: '101019' }})">
            <section class="icon"><img width="40px" src="./outage.png"/></section>
            <section class="data">{{losePowerNo}}</section>
            <section class="data">断电</section>
          </article>
          <article @click="handlePush({path: '/main/alarm/deviceAlert', name: '位移报警', props: { typeid: '103025' }})">
            <section class="icon"><img width="40px" src="./move.png"/></section>
            <section class="data">{{displacementNo}}</section>
            <section class="data">位移</section>
          </article>
          <article @click="handlePush({path: '/main/alarm/deviceAlert', name: '拆卸报警', props: { typeid: '103040' } })">
            <section class="icon"><img height="40px" src="./img_stop.png"/></section>
            <section class="data">{{disassembleNo}}</section>
            <section class="data">拆卸报警</section>
          </article>
          <article @click="handlePush({path: '/main/alarm/deviceAlert', name: '24小时无信号', props: { typeid: '103020' } })">
            <section class="icon"><img width="40px" src="./connectless.png"/></section>
            <section class="data">{{noSingleNo}}</section>
            <section class="data">24小时无信号</section>
          </article>
          <article @click="handlePush({path: '/main/alarm/deviceAlert', name: '三天无行驶', props:{ typeid: '5' }})">
            <section class="icon"><img width="40px" src="./pack.png"/></section>
            <section class="data">{{noTravelNo}}</section>
            <section class="data">三天无行驶</section>
          </article>
        </el-card>
      </el-col>

      <el-col :span="4">
        <el-card class="box-card system-notice">
          <section slot="header">
            <h1>
              <i class="fa fa-bullhorn" aria-hidden="true"></i> &nbsp;系统公告
            </h1>
          </section>
          <section>
            <el-carousel trigger="click" height="170px">
              <el-carousel-item v-for="(item,  index) in noticeBoard" :key="index" class="">
                <h3>{{index + 1}}. {{item}}</h3>
              </el-carousel-item>
            </el-carousel>
          </section>
        </el-card>
      </el-col>
    </section>

    <section class="m-t-15">
      <el-col>
        <el-row :gutter="gutter">
          <el-col :span="12">
            <el-card class="box-card" v-loading="vehicleStatusLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-bar-chart" aria-hidden="true"></i>
                  &nbsp;
                  设备统计报表&nbsp;<i class="a-text-deco f-w-b pointer">(总设备:{{deviceNum * multiple}})</i>
                </h1>
                &nbsp;<i @click="fetchVehicleStatus" :class="{ 'fa-spin': vehicleStatusLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
              </div>
              <chart :options="vehicleStatusReport" ref="vehicleStatusReport"  style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
          <el-col :span="12">
            <el-card class="box-card" v-loading="onlineHistoryLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-line-chart" aria-hidden="true"></i>
                  &nbsp;
                  设备在线历史报表
                </h1>
                &nbsp;<i @click="fetchOnlineHistory" :class="{ 'fa-spin': onlineHistoryLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
              </div>
              <chart :options="onlineHistoryReport" ref="onlineHistoryReport"  style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
        </el-row>

        <el-row :gutter="gutter" class="m-t-15">
          <el-col :span="12">
            <el-card class="box-card" v-loading="offlineHistoryLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-line-chart" aria-hidden="true"></i>
                  &nbsp;
                  设备离线历史报表
                </h1>
                &nbsp;<i @click="fetchOfflineHistory" :class="{ 'fa-spin': offlineHistoryLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
              </div>
              <chart :options="offlineHistoryReport" ref="offlineHistoryReport"  style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
          <el-col :span="12">
            <el-card class="box-card" v-loading="newDevicesLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-line-chart" aria-hidden="true"></i>
                  &nbsp;
                  新增入库设备数
                </h1>
                <div>
                  <el-select @change="fetchNewDevices" style="width: 80px" size="mini" v-model="newDevicesParams.period" placeholder="请选择日期类型">
                    <el-option
                      v-for="item in periodOptions"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value">
                    </el-option>
                  </el-select>
                  &nbsp;<i @click="fetchNewDevices" :class="{ 'fa-spin': newDevicesLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
                </div>
              </div>
              <chart :options="newDevicesLine" ref="newDevices" style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
        </el-row>

        <el-row :gutter="gutter" class="m-t-15">
          <el-col :span="12">
            <el-card class="box-card" v-loading="activeDevicesLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-line-chart" aria-hidden="true"></i>
                  &nbsp;
                  新增激活设备数
                </h1>
                <div>
                  <el-select @change="fetchActiveDevices" style="width: 80px" size="mini" v-model="activeDevicesParams.period" placeholder="请选择日期类型">
                    <el-option
                      v-for="item in periodOptions"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value">
                    </el-option>
                  </el-select>
                  &nbsp;<i @click="fetchActiveDevices" :class="{ 'fa-spin': activeDevicesLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
                </div>
              </div>
              <chart :options="activeDevicesLine" ref="activeDevices" @click="handleActiveDevicesDetail" style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
          <el-col :span="12">
            <el-card class="box-card" v-loading="dailyOilLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-line-chart" aria-hidden="true"></i>
                  &nbsp;
                  总车辆油耗报表(L)
                </h1>
                <div>
                  <el-radio-group @change="fetchDailyOil" size="mini" v-model="dailyOilParams.period">
                    <el-radio-button :label="3">本周</el-radio-button>
                    <el-radio-button :label="4">上周</el-radio-button>
                  </el-radio-group>
                  &nbsp;<i @click="fetchDailyOil" :class="{ 'fa-spin': dailyOilLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
                </div>
              </div>
              <chart :options="dailyOilLine" ref="dailyOil" style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
        </el-row>

        <el-row :gutter="gutter" class="m-t-15">
          <el-col :span="12">
            <el-card class="box-card" v-loading="dailyMileageLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-line-chart" aria-hidden="true"></i>
                  &nbsp;
                  总车辆里程报表(KM)
                </h1>
                <div>
                  <el-radio-group @change="fetchDailyMileage" size="mini" v-model="dailyMileageParams.period">
                    <el-radio-button :label="3">本周</el-radio-button>
                    <el-radio-button :label="4">上周</el-radio-button>
                  </el-radio-group>
                  &nbsp;<i @click="fetchDailyMileage" :class="{ 'fa-spin': dailyMileageLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
                </div>
              </div>
              <chart :options="dailyMileageLine" ref="dailyMileage" style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
          <el-col :span="12">
            <el-card class="box-card" v-loading="monthReportLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-bar-chart" aria-hidden="true"></i>
                  &nbsp;
                  各类月报表
                </h1>
                <div>
                  <el-select @change="fetchMonthReport" style="width: 80px" size="mini" v-model="monthReportParams.period" placeholder="请选择日期类型">
                    <el-option
                      v-for="item in periodOptions"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value">
                    </el-option>
                  </el-select>
                  &nbsp;<i @click="fetchMonthReport" :class="{ 'fa-spin': monthReportLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
                </div>
              </div>
              <chart :options="monthReportBar" ref="monthReportBar" style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
        </el-row>

        <el-row :gutter="gutter"class="m-t-15">
          <el-col :span="12">
            <el-card class="box-card" v-loading="drivingBehaviorLoading" element-loading-text="拼命加载中...">
              <div slot="header">
                <h1>
                  <i class="fa fa-bar-chart" aria-hidden="true"></i>
                  &nbsp;
                  驾驶行为报表
                </h1>
                <div>
                  <el-select @change="fetchDrivingBehavior" style="width: 80px" size="mini" v-model="drivingBehaviorParams.period" placeholder="请选择日期类型">
                    <el-option
                      v-for="item in periodOptions"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value">
                    </el-option>
                  </el-select>
                  &nbsp;<i @click="fetchDrivingBehavior" :class="{ 'fa-spin': drivingBehaviorLoading }" class="fa fa-refresh pointer" aria-hidden="true"></i>
                </div>
              </div>
              <chart :options="drivingBehaviorBar" ref="drivingBehavior" style="width: 100%; height: 170px"></chart>
            </el-card>
          </el-col>
        </el-row>
      </el-col>

      <el-col>
        <el-card class="box-card">
          <section slot="header">
            <h1>
              <i class="fa fa-bug" aria-hidden="true"></i> &nbsp;系统报警
            </h1>
            <div>
              <el-select @change="alertListParams.curPage = 1;fetchAlertList()" style="width: 80px" size="mini" v-model="alertListParams.timeType" placeholder="请选择日期类型">
                <el-option
                  v-for="item in periodOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
          </section>
          <section>
            <div v-if="alertList.length">
              <ul class="alarm-list">
                <li v-for="({ devicename, alerttime, typename, typeid, address }, index) in alertList"
                    :key="index"
                    @click="() => $handleAlarmClick(typeid, alertListParams.timeType)">
                  <div class="title"><h3>{{devicename}}</h3> <span class="type" :style="{ color: $dangerTypes.indexOf(typeid) !== -1 ? 'red' : '#FF9933' }">{{typename}}</span></div>
                  <p class="time">时间: {{alerttime}}</p>
                  <p class="position">位置: {{address}}</p>
                </li>
              </ul>
              <el-pagination
                small
                class="tx-c"
                layout="prev, pager, next"
                :total="alertListParams.counts"
                :current-page.sync="alertListParams.curPage"
                @current-change="fetchAlertList">
              </el-pagination>
            </div>
            <p v-else class="tx-c c-gra">暂无数据</p>
          </section>
        </el-card>
      </el-col>
    </section>
    <active-devices-detail ref="activeDevicesDetail"></active-devices-detail>
  </div>
</template>

<script>
import http from '@/assets/js/http';
import echarts from 'echarts/lib/echarts';
import 'echarts/lib/chart/bar';
import 'echarts/lib/chart/line';
import 'echarts/lib/component/tooltip';

import ActiveDevicesDetail from './active-devices-detail';

export default {
  name: escapeName('首页'),
  mixins: [http],
  components: {
    ActiveDevicesDetail,
  },
  computed: {
    multiple() {
      return Lockr.get('securId') === 2 ? 1 : 1;
    },
  },
  data() {
    return {
      alertList: [],
      noticeBoard: [
        `热烈祝贺${this.$configuration.title}风控平台2.2上线成功!`,
        `热烈祝贺${this.$configuration.title}APP上线成功!`,
      ],
      gutter: 15,
      newDevicesParams: {
        period: 3,
      },
      activeDevicesParams: {
        period: 3,
      },
      dailyOilParams: {
        period: 3,
      },
      dailyMileageParams: {
        period: 3,
      },
      monthReportParams: {
        period: 3,
      },
      drivingBehaviorParams: {
        period: 3,
      },
      alertListParams: {
        alertstatus: 0,
        timeType: 1,
        pageSize: 10,
        status: 1,
        counts: 0,
        curPage: 1,
      },
      periodOptions: [
        { label: '今天', value: 1 },
        { label: '昨天', value: 2 },
        { label: '本周', value: 3 },
        { label: '上周', value: 4 },
        { label: '本月', value: 5 },
        { label: '上月', value: 6 },
      ],
      deviceNum: 0,
      displacementNo: null,
      disassembleNo: null,
      noTravelNo: null,
      twoPointNo: null,
      losePowerNo: null,
      noSingleNo: null,

      dailyMileageLoading: false,
      dailyOilLoading: false,
      newDevicesLoading: false,
      drivingBehaviorLoading: false,
      monthReportLoading: false,
      deviceAlertLoading: false,
      vehicleStatusLoading: false,
      onlineHistoryLoading: false,
      offlineHistoryLoading: false,
      activeDevicesLoading: false,

      dailyOilLine: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B',
            },
          },
        },
        legend: {
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          data: ['油耗'],
          right: '4%',
          // textStyle: {
          //   fontSize: 12,
          //   color: '#F1F1F3',
          // },
        },
        grid: {
          left: 5,
          right: 35,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
            axisLabel: {
              color: '#000',
            },
            data: [
              '13:00',
              '13:05',
              '13:10',
              '13:15',
              '13:20',
              '13:25',
              '13:30',
              '13:35',
              '13:40',
              '13:45',
              '13:50',
              '13:55',
            ],
          },
        ],
        yAxis: [
          {
            type: 'value',
            name: '单位(L)',
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: '#57617B',
              },
            },
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: 14,
              },
            },
            splitLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
        ],
        series: [
          {
            name: '油耗',
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 5,
            showSymbol: false,
            lineStyle: {
              normal: {
                width: 1,
              },
            },
            areaStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(
                  0,
                  0,
                  0,
                  1,
                  [
                    {
                      offset: 0,
                      color: '#20A0FF',
                    },
                    {
                      offset: 0.8,
                      color: 'rgba(32, 160, 255, 0)',
                    },
                  ],
                  false,
                ),
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 10,
              },
            },
            itemStyle: {
              normal: {
                color: '#20A0FF',
                borderColor: 'rgba(32, 160, 255, 0.2)',
                borderWidth: 12,
              },
            },
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
        ],
      },
      dailyMileageLine: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B',
            },
          },
        },
        legend: {
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          data: ['里程'],
          right: '4%',
          // textStyle: {
          //   fontSize: 12,
          //   color: '#F1F1F3',
          // },
        },
        grid: {
          left: 5,
          right: 35,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
            axisLabel: {
              color: '#000',
            },
            data: [
              '13:00',
              '13:05',
              '13:10',
              '13:15',
              '13:20',
              '13:25',
              '13:30',
              '13:35',
              '13:40',
              '13:45',
              '13:50',
              '13:55',
            ],
          },
        ],
        yAxis: [
          {
            type: 'value',
            name: '单位(KM)',
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: '#57617B',
              },
            },
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: 14,
              },
            },
            splitLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
        ],
        series: [
          {
            name: '里程',
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 5,
            showSymbol: false,
            lineStyle: {
              normal: {
                width: 1,
              },
            },
            areaStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(
                  0,
                  0,
                  0,
                  1,
                  [
                    {
                      offset: 0,
                      color: '#58B7FF',
                    },
                    {
                      offset: 0.8,
                      color: 'rgba(88, 183, 255, 0)',
                    },
                  ],
                  false,
                ),
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 10,
              },
            },
            itemStyle: {
              normal: {
                color: '#58B7FF',
                borderColor: 'rgba(88, 183, 255, .2)',
                borderWidth: 12,
              },
            },
            data: [220, 182, 191, 134, 150, 120, 110, 125, 145, 122, 165, 122],
          },
        ],
      },
      newDevicesLine: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B',
            },
          },
        },
        legend: {
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          data: ['入库设备'],
          right: '4%',
          // textStyle: {
          //   fontSize: 12,
          //   color: '#F1F1F3',
          // },
        },
        grid: {
          left: 5,
          right: 35,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
            axisLabel: {
              color: '#000',
            },
            data: [
              '13:00',
              '13:05',
              '13:10',
              '13:15',
              '13:20',
              '13:25',
              '13:30',
              '13:35',
              '13:40',
              '13:45',
              '13:50',
              '13:55',
            ],
          },
        ],
        yAxis: [
          {
            type: 'value',
            name: '单位(个)',
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: '#57617B',
              },
            },
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: 14,
              },
            },
            splitLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
        ],
        series: [
          {
            name: '入库设备',
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 5,
            showSymbol: false,
            lineStyle: {
              normal: {
                width: 1,
              },
            },
            areaStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(
                  0,
                  0,
                  0,
                  1,
                  [
                    {
                      offset: 0,
                      color: 'rgba(29, 140, 224, 0.3)',
                    },
                    {
                      offset: 0.8,
                      color: 'rgba(29, 140, 224, 0)',
                    },
                  ],
                  false,
                ),
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 10,
              },
            },
            itemStyle: {
              normal: {
                color: '#1D8CE0',
                borderColor: 'rgba(29, 140, 224, 0.2)',
                borderWidth: 12,
              },
            },
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
        ],
      },
      activeDevicesLine: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B',
            },
          },
        },
        legend: {
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          data: ['激活设备'],
          right: '4%',
          // textStyle: {
          //   fontSize: 12,
          //   color: '#F1F1F3',
          // },
        },
        grid: {
          left: 5,
          right: 35,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
            axisLabel: {
              color: '#000',
            },
            data: [
              '13:00',
              '13:05',
              '13:10',
              '13:15',
              '13:20',
              '13:25',
              '13:30',
              '13:35',
              '13:40',
              '13:45',
              '13:50',
              '13:55',
            ],
          },
        ],
        yAxis: [
          {
            type: 'value',
            name: '单位(个)',
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: '#57617B',
              },
            },
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: 14,
              },
            },
            splitLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
        ],
        series: [
          {
            name: '激活设备',
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 5,
            showSymbol: false,
            lineStyle: {
              normal: {
                width: 1,
              },
            },
            areaStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(
                  0,
                  0,
                  0,
                  1,
                  [
                    {
                      offset: 0,
                      color: 'rgba(29, 140, 224, 0.3)',
                    },
                    {
                      offset: 0.8,
                      color: 'rgba(29, 140, 224, 0)',
                    },
                  ],
                  false,
                ),
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 10,
              },
            },
            itemStyle: {
              normal: {
                color: '#1D8CE0',
                borderColor: 'rgba(29, 140, 224, 0.2)',
                borderWidth: 12,
              },
            },
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
        ],
      },
      monthReportBar: {
        grid: {
          left: 0,
          right: 0,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        tooltip: {
          trigger: 'item',
        },
        // dataRange: {
        //   show: false,
        //   min: 0,
        //   max: 12,
        //   calculable: true,
        //   color: ['#d94e5d', '#eac736', '#50a3ba'],
        //   y: 'center',
        // },

        xAxis: [
          {
            type: 'category',
            show: false,
            data: ['里程(km)', '油耗(L)', '急加速(次)', '急减速(次)', '急转弯(次)'],
          },
        ],
        yAxis: [
          {
            type: 'value',
            show: false,
          },
        ],
        series: [
          {
            name: '各类月报表',
            type: 'bar',
            barGap: 0.1,
            barCategoryGap: 0.01,
            itemStyle: {
              normal: {
                color(params) {
                  // build a color map as your need.
                  const colorList = [
                    '#58B7FF',
                    '#20A0FF',
                    '#1D8CE0',
                  ];
                  return colorList[params.dataIndex];
                },
                label: {
                  show: true,
                  position: 'top',
                  formatter: '{b}\n{c}',
                },
                shadowBlur: 10,
                shadowColor: 'rgba(0, 0, 0, 0.5)',
              },
            },
            data: [5, 8, 12, 14, 16],
          },
        ],
      },
      drivingBehaviorBar: {
        grid: {
          left: 0,
          right: 0,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        tooltip: {
          trigger: 'item',
        },
        // dataRange: {
        //   show: false,
        //   min: 0,
        //   max: 12,
        //   calculable: true,
        //   color: ['#eac736', '#50a3ba'],
        //   y: 'center',
        // },

        xAxis: [
          {
            type: 'category',
            show: false,
            data: ['超速累计时长(秒)', '超速(次)', '累计怠速时长(秒)', '急加速(次)', '急减速(次)', '急转弯(次)'],
          },
        ],
        yAxis: [
          {
            type: 'value',
            show: false,
          },
        ],
        series: [
          {
            name: '驾驶行为报表',
            type: 'bar',
            barGap: 0.1,
            barCategoryGap: 0.01,
            itemStyle: {
              normal: {
                color(params) {
                  // build a color map as your need.
                  const colorList = ['#86c9f4', '#4da8ec', '#3a91d2', '#005fa6', '#315f97'];
                  return colorList[params.dataIndex];
                },
                label: {
                  show: true,
                  position: 'top',
                  formatter: '{b}\n{c}',
                },
                shadowBlur: 10,
                shadowColor: 'rgba(0, 0, 0, 0.5)',
              },
            },
            data: [5, 8, 12, 14, 16, 18, 20],
          },
        ],
      },
      vehicleStatusReport: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow',
          },
        },
        color: ['#58B7FF', '#20A0FF', '#1D8CE0'],
        grid: {
          left: 0,
          right: 0,
          bottom: 0,
          top: 0,
          containLabel: true,
        },
        xAxis: {
          type: 'value',
          boundaryGap: [0, 0.01],
          axisLine: {
            show: false,
            lineStyle: {
              color: '#57617B',
            },
          },
          axisTick: {
            show: false,
          },
        },
        yAxis: {
          type: 'category',
          data: ['ZY-M', 'ZY-T', 'ZY-V', '离线', '在线'],
          axisLine: {
            lineStyle: {
              color: '#ccc',
            },
          },
          axisLabel: {
            color: '#000',
          },
        },
        series: [
          {
            name: '数量',
            type: 'bar',
            data: [0, 0, 0, 0, 0],
            itemStyle: {
              normal: {
                label: {
                  show: true,
                  position: 'right',
                },
              },
            },
          },
        ],
      },
      onlineHistoryReport: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B',
            },
          },
        },
        legend: {
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          data: ['激活设备'],
          right: '4%',
          // textStyle: {
          //   fontSize: 12,
          //   color: '#F1F1F3',
          // },
        },
        grid: {
          left: 5,
          right: 20,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
            axisLabel: {
              color: '#000',
            },
            data: [
              '13:00',
              '13:05',
              '13:10',
              '13:15',
              '13:20',
              '13:25',
              '13:30',
              '13:35',
              '13:40',
              '13:45',
              '13:50',
              '13:55',
            ],
          },
        ],
        yAxis: [
          {
            type: 'value',
            name: '单位(个)',
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: '#57617B',
              },
            },
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: 14,
              },
            },
            splitLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
        ],
        series: [
          {
            name: '在线设备',
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 5,
            showSymbol: false,
            lineStyle: {
              normal: {
                width: 1,
              },
            },
            areaStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(
                  0,
                  0,
                  0,
                  1,
                  [
                    {
                      offset: 0,
                      color: 'rgba(19, 206, 102, 0.3)',
                    },
                    {
                      offset: 0.8,
                      color: 'rgba(19, 206, 102, 0)',
                    },
                  ],
                  false,
                ),
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 10,
              },
            },
            itemStyle: {
              normal: {
                color: 'rgba(19, 206, 102, 1)',
                borderColor: 'rgba(19, 206, 102, 0.2)',
                borderWidth: 12,
              },
            },
            data: [220, 182, 191, 134, 150, 120, 110, 125, 145, 122, 165, 122],
          },
        ],
      },
      offlineHistoryReport: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B',
            },
          },
        },
        legend: {
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          data: ['离线设备'],
          right: '4%',
          // textStyle: {
          //   fontSize: 12,
          //   color: '#F1F1F3',
          // },
        },
        grid: {
          left: 5,
          right: 20,
          bottom: 0,
          top: '30px',
          containLabel: true,
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
            axisLabel: {
              color: '#000',
            },
            data: [
              '13:00',
              '13:05',
              '13:10',
              '13:15',
              '13:20',
              '13:25',
              '13:30',
              '13:35',
              '13:40',
              '13:45',
              '13:50',
              '13:55',
            ],
          },
        ],
        yAxis: [
          {
            type: 'value',
            name: '单位(个)',
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false,
              lineStyle: {
                color: '#57617B',
              },
            },
            axisLabel: {
              margin: 10,
              textStyle: {
                fontSize: 14,
              },
            },
            splitLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
        ],
        series: [
          {
            name: '离线设备',
            type: 'line',
            smooth: true,
            symbol: 'circle',
            symbolSize: 5,
            showSymbol: false,
            lineStyle: {
              normal: {
                width: 1,
              },
            },
            areaStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(
                  0,
                  0,
                  0,
                  1,
                  [
                    {
                      offset: 0,
                      color: 'rgba(19, 206, 102, 0.3)',
                    },
                    {
                      offset: 0.8,
                      color: 'rgba(19, 206, 102, 0)',
                    },
                  ],
                  false,
                ),
                shadowColor: 'rgba(0, 0, 0, 0.1)',
                shadowBlur: 10,
              },
            },
            itemStyle: {
              normal: {
                color: 'rgba(19, 206, 102, 1)',
                borderColor: 'rgba(19, 206, 102, 0.2)',
                borderWidth: 12,
              },
            },
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
        ],
      },
    };
  },
  mounted() {
    this.$nextTick(() => {
      setTimeout(() => {
        this.resizeCharts();
      }, 1000);
      window.onresize = () => {
        if (this.$refs.homePageWrapper.parentNode.style.display === 'none') return;
        this.resizeCharts();
      };
      this.fetchVehicleStatus();
      this.fetchDeviceAlertCount();
      this.fetchOnlineHistory();
      this.fetchOfflineHistory();
      this.fetchNewDevices();
      this.fetchActiveDevices();
      this.fetchDailyMileage();
      this.fetchDailyOil();
      this.fetchDrivingBehavior();
      this.fetchMonthReport();
      this.fetchAlertList();
    });
  },
  destoryed() {
    window.onresize = null;
  },
  methods: {
    handleActiveDevicesDetail({ name }) {
      this.$refs.activeDevicesDetail.open(name);
    },
    resizeCharts() {
      for (const i in this.$refs) {
        if (typeof this.$refs[i].resize === 'function' && this.$refs[i].$el.offsetWidth !== 0) {
          this.$refs[i].resize();
        }
      }
    },
    async fetchAlertList() {
      const { data, page: { counts } } = await this.apiPost('/deviceAlert/queryAlertList', this.alertListParams);
      this.alertList = data;
      this.alertListParams.counts = counts;
    },
    async fetchVehicleStatus() {
      this.vehicleStatusLoading = true;
      try {
        const { data: { zymNo, zytNo, zyvNo, totalNo, onlineNo, offlineNo } } = await this.apiPost('/device/getDeviceNumber', {});
        this.deviceNum = totalNo;
        this.vehicleStatusReport.series[0].data = [
          zymNo,
          zytNo,
          zyvNo,
          offlineNo,
          onlineNo,
        ].map(i => i * this.multiple);
        this.vehicleStatusLoading = false;
      } catch (e) {
        this.vehicleStatusLoading = false;
      }
    },
    async fetchDeviceAlertCount() {
      this.deviceAlertLoading = true;
      try {
        const { data: { displacementNo, disassembleNo, noTravelNo, twoPointNo, losePowerNo, noSingleNo } } = await this.apiPost('/deviceAlert/getAlertCount', {});
        this.displacementNo = displacementNo * this.multiple;
        this.disassembleNo = disassembleNo * this.multiple;
        this.noTravelNo = noTravelNo * this.multiple;
        this.twoPointNo = twoPointNo * this.multiple;
        this.losePowerNo = losePowerNo * this.multiple;
        this.noSingleNo = noSingleNo * this.multiple;
        this.deviceAlertLoading = false;
      } catch (e) {
        this.deviceAlertLoading = false;
      }
    },
    async fetchOnlineHistory() {
      this.onlineHistoryLoading = true;
      try {
        const { data } = await this.apiPost('/device/getDeviceOnlineCount', { period: 1 });
        this.onlineHistoryReport.xAxis[0].data = data.map(i => i.reportDate);
        this.onlineHistoryReport.series[0].data = data.map(i => i.reportData * this.multiple);
        this.onlineHistoryLoading = false;
      } catch (e) {
        this.onlineHistoryLoading = false;
      }
    },
    async fetchOfflineHistory() {
      this.offlineHistoryLoading = true;
      try {
        const { data } = await this.apiPost('/device/getDeviceOfflineCount', { period: 1 });
        this.offlineHistoryReport.xAxis[0].data = data.map(i => i.reportDate);
        this.offlineHistoryReport.series[0].data = data.map(i => i.reportData * this.multiple);
        this.offlineHistoryLoading = false;
      } catch (e) {
        this.offlineHistoryLoading = false;
      }
    },
    async fetchDailyOil() {
      this.dailyOilLoading = true;
      try {
        const { data } = await this.apiPost('/obd/getOilWeekReport', this.dailyOilParams);
        const x = data.map(i => i.reportDate);
        const y = data.map(i => i.reportData);
        this.dailyOilLine.xAxis[0].data = x;
        this.dailyOilLine.series[0].data = y;
        this.dailyOilLoading = false;
      } catch (e) {
        this.dailyOilLoading = false;
      }
    },
    async fetchDailyMileage() {
      this.dailyMileageLoading = true;
      try {
        const { data } = await this.apiPost('/obd/getMilWeekReport', this.dailyMileageParams);
        const x = data.map(i => i.reportDate);
        const y = data.map(i => i.reportData);
        this.dailyMileageLine.xAxis[0].data = x;
        this.dailyMileageLine.series[0].data = y;
        this.dailyMileageLoading = false;
      } catch (e) {
        this.dailyMileageLoading = false;
      }
    },
    async fetchNewDevices() {
      this.newDevicesLoading = true;
      try {
        const { data } = await this.apiPost('/device/getNewDeviceInfo', this.newDevicesParams);
        const x = data.map(i => i.addDate);
        const y = data.map(i => i.addDeviceNumber * this.multiple);
        this.newDevicesLine.xAxis[0].data = x;
        this.newDevicesLine.series[0].data = y;
        this.newDevicesLoading = false;
      } catch (e) {
        this.newDevicesLoading = false;
      }
    },
    async fetchActiveDevices() {
      this.activeDevicesLoading = true;
      try {
        const { data } = await this.apiPost('/device/getActiDeviceInfo', this.activeDevicesParams);
        const x = data.map(i => i.addDate);
        const y = data.map(i => i.addDeviceNumber * this.multiple);
        this.activeDevicesLine.xAxis[0].data = x;
        this.activeDevicesLine.series[0].data = y;
        this.activeDevicesLoading = false;
      } catch (e) {
        this.activeDevicesLoading = false;
      }
    },
    async fetchMonthReport() {
      this.monthReportLoading = true;
      try {
        const { data: {
          totalMil,
          totalOil,
          totalAccCount,
          totalDecCount,
          totalShpturnCount,
          } } = await this.apiPost('/obd/getMonthReportForm', this.monthReportParams);
        this.monthReportBar.series[0].data = [
          totalMil,
          totalOil,
          totalAccCount,
          totalDecCount,
          totalShpturnCount,
        ];
        this.monthReportLoading = false;
      } catch (e) {
        this.monthReportLoading = false;
      }
    },
    async fetchDrivingBehavior() {
      this.drivingBehaviorLoading = true;
      try {
        const { data: {
          totalOverspdDur,
          totalOverspdCount,
          totalIdleDur,
          totalAccCount,
          totalDecCount,
          totalShpturnCount,
          } } = await this.apiPost('/obd/getTotalReportForm', this.drivingBehaviorParams);
        this.drivingBehaviorBar.series[0].data = [
          totalOverspdDur,
          totalOverspdCount,
          totalIdleDur,
          totalAccCount,
          totalDecCount,
          totalShpturnCount,
        ];
        this.drivingBehaviorLoading = false;
      } catch (e) {
        this.drivingBehaviorLoading = false;
      }
    },
    handlePush(menu) {
      window.$routerPush(menu);
    },
  },
};
</script>

<style lang="stylus">
  .homePage-wrapper
    height: 100%
    padding: 20px
    // background-image: linear-gradient(281deg, #b8b4cc 0%, #63d3e2 100%)
    background: rgba(240, 240, 240, 1)
    color: #555
    >section
      display: flex
      >div:first-child
        flex: 1 1 auto
        width: 0
        margin-right: 15px
      >div:last-child
        flex: 0 0 250px
    .box-card
      margin: 0px 0px 1px
      &.system-notice
        .el-card__body
          padding: 0px
          h3
            padding: 0px 15px
            margin-top: 50px
            line-height: 20px
            text-align: center
      .el-card__header
        line-height: 22px
        >div, section, h1
          display: flex
          align-items: center
          justify-content: space-between
          font-weight: bold
      .alarm-list
        li
          margin-top: 10px
          padding-bottom: 10px
          border-bottom: 1px solid #ccc
          cursor: pointer
          transition: all .2s
          &:hover
            text-decoration: underline
            // background: rgba(0, 0, 0, .2)
          &:first-child
            margin-top: 0px
          &:last-child
            padding-bottom: 0px
            border-bottom: none
          div, p
            line-height: 20px
          .title
            display: flex
            justify-content: space-between
            h3
              font-size: 16px
              font-weight: bold
            .type
              font-size: 14px
      &.system-info
        .el-card__body
          display: flex
          article
            display: flex
            flex-direction: column
            align-items: center
            justify-content: center
            margin-right: 15px
            flex: 1 1 18%
            height: 130px
            border-radius: 5px
            cursor: pointer
            transition: all .2s
            text-align: center
            &:last-child
              margin-right: 0px
            >section
              flex: 1 1 33%
              line-height: 43px
              color: #fff
              &:nth-child(1)
                line-height: 50px
                img
                  vertical-align: middle
              &:nth-child(2)
                line-height: 48px
                overflow: hidden
                font-weight: bold
                font-size: 24px
              &:nth-child(3)
                font-size: 14px
            &:nth-child(1)
              background: #32B4FA
            &:nth-child(2)
              background: #33CC00
            &:nth-child(3)
              background: #797979
            &:nth-child(4)
              background: #FF9933
            &:nth-child(5)
              background: #669999
            &:nth-child(6)
              background: #FC8556
            &:hover
              opacity: .70

</style>
