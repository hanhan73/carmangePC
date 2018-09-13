<template>
  <div class="trackReplay-wrapper ovf-hd">
    <div
      class="loading animated"
      v-show="isLoading"
      :class="{ bounceIn: isLoading }">
      <i class="el-icon-loading"></i>
      <span class="text">&nbsp;拼命加载中...</span>
    </div>
    <div class="map">
      <el-amap  vid="trackReplay"
                :events="events"
                :animateEnable="false"
                :resizeEnable="false"
                :jogEnable="false"
                ref="amap"
                class="ovf-hd"
                >
        <el-amap-marker v-for="(marker, index) in markers"
                        :key="'marker' + index"
                        :vid="'marker' + index"
                        :topWhenClick="true"
                        :position="marker.position"
                        :animation="marker.animation"
                        :icon="marker.icon"
                        :template="marker.template || ''"
                        :offset="marker.offset ? marker.offset : [-10,-34]"
                        :angle="marker.angle"
                        :title="marker.title"
                        :events="marker.events"
                        :extData="marker.extData"
                        :visible="marker.visible"
                        :draggable="marker.draggable">
        </el-amap-marker>
        <el-amap-marker v-for="(marker, index) in stayMarkers"
                        :key="'stayMarker' + index"
                        :vid="'stayMarker' + index"
                        :topWhenClick="true"
                        :position="marker.position"
                        :animation="marker.animation"
                        :icon="marker.icon"
                        :template="marker.template || ''"
                        :offset="marker.offset ? marker.offset : [-10,-34]"
                        :angle="marker.angle"
                        :title="marker.title"
                        :events="marker.events"
                        :extData="marker.extData"
                        :visible="marker.visible"
                        :draggable="marker.draggable">
        </el-amap-marker>
        <el-amap-marker v-if="infoMarker.id"
                        :vid="infoMarker.id"
                        :key="infoMarker.id"
                        :topWhenClick="true"
                        :position="infoMarker.position"
                        :content="infoMarker.content"
                        :offset="[-106, -123]"
                        :zIndex="200"
                        :visible="true">
        </el-amap-marker>
        <!-- <el-amap-marker v-if="alertMarker.id"
                        :vid="alertMarker.id"
                        :key="alertMarker.id"
                        :topWhenClick="true"
                        :position="alertMarker.position"
                        :visible="true">
        </el-amap-marker> -->
        <el-amap-info-window v-for="(win, index) in windows"
                              :key="'win' + win.id"
                              :position="win.position"
                              :template="win.template"
                              :visible="win.visible"
                              :offset="win.offset || [0, 0]"
                              :autoMove="true"
                              :events="win.events || {}">
        </el-amap-info-window>
        <el-amap-circle v-for="(circle, index) in circles"
                        :key="index"
                        :strokeColor="'#1791fc'"
                        :strokeOpacity="0.8"
                        :strokeWeight="2"
                        :fillColor="'#1791fc'"
                        :fillOpacity="0.35"
                        :center="circle.center"
                        :radius="circle.radius">
        </el-amap-circle>
        <el-amap-polygon v-for="(polygon, index) in polygons"
                          :key="index"
                          :vid="index"
                          :path="polygon.path"
                          :strokeColor="'#1791fc'"
                          :strokeOpacity="0.8"
                          :strokeWeight="2"
                          :fillColor="'#1791fc'"
                          :fillOpacity="0.35">
        </el-amap-polygon>
        <el-amap-polyline
          :zIndex="1000"
          :strokeWeight="polyline.strokeWeight"
          :strokeColor="polyline.strokeColor"
          :editable="polyline.editable"
          :path="polyline.path" >
        </el-amap-polyline>
      </el-amap>
    </div>
    <div class="table">
      <div class="shrink-top" @click="monitorBottomShrink = !monitorBottomShrink"><i :class="`el-icon-arrow-${monitorBottomShrink ? 'up' : 'down'}`"></i></div>
      <div id="trackBottom" :style="{ 'height': monitorBottomShrink ? '0px' : '150px' }">
        <el-table size="mini" class="w-100p h-100p" height="auto" :data="tableData" ref="table" highlight-current-row @row-click="handleRowClick">
          <el-table-column width="80" type="index"></el-table-column>
          <el-table-column width="170" prop="devicename" label="设备名称"></el-table-column>
          <!-- <el-table-column width="170" prop="dateMil" label="日里程(KM)"></el-table-column> -->
          <el-table-column width="170" prop="totalMil" label="总里程(KM)"></el-table-column>
          <el-table-column width="170" prop="gpsinfo.gpstime" label="定位时间"></el-table-column>
          <el-table-column width="100" prop="locationMode" label="定位方式"></el-table-column>
          <el-table-column width="100" label="速度" :formatter="({ gpsinfo: { speed } }) => `${speed} km/h`"></el-table-column>
          <el-table-column width="100" label="方向" prop="directionText"></el-table-column>
          <el-table-column show-overflow-tooltip label="位置" :render-header="positionRenderHeader">
            <template slot-scope="{ row: { gpsinfo: { lng, lat }, isTransform, address }, $index }">
              <el-button type="text" size="mini" icon="el-icon-location" @click.stop="transformLngLat($index)">{{isTransform ? '位置' : '解析'}}</el-button>
              <span v-if="!isTransform">经度:({{lng}}),纬度:({{lat}})</span>
              <span v-else>地址: {{address}}</span>
            </template>
          </el-table-column>
          <el-table-column label="状态" prop="gpsinfo.state"></el-table-column>
          <!-- <el-table-column label="设备名称">
            <span>dssadas</span>
          </el-table-column> -->
        </el-table>
      </div>
    </div>
    <el-popover
      ref="trackBtn"
      placement="left"
      width="650"
      trigger="click">
      <div class="player-wrapper">
        <el-date-picker
          size="mini"
          type="datetime"
          style="width: 173px"
          v-model="params.sTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="选择开始日期">
        </el-date-picker>
        <el-date-picker
          size="mini"
          type="datetime"
          style="width: 173px; margin-left: 10px"
          v-model="params.eTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="选择结束日期">
        </el-date-picker>
        <!-- <el-date-picker
          size="mini"
          style="width: 315px"
          v-model="time"
          type="datetimerange"
          placeholder="选择日期范围"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          align="right"
          :default-value="defaultValue"
          :picker-options="pickerOptions">
        </el-date-picker> -->
        <el-radio-group size="mini" v-model="params.locationType" class="locationRatio">
          <el-radio-button :label="''">全部</el-radio-button>
          <el-radio-button :label="1">GPS</el-radio-button>
          <el-radio-button :label="0">基站</el-radio-button>
          <el-radio-button :label="2">WIFI</el-radio-button>
        </el-radio-group>
        <el-button type="primary"
                    size="mini"
                    :icon="`el-icon-${isLoading ? 'loading' : 'search'}`"
                    :disabled="isLoading"
                    @click="showTrack">
        </el-button>
        <el-progress class="progress"
                    status="success"
                    :stroke-width="3"
                    :show-text="false"
                    :percentage="percent"
                    >
        </el-progress>
        <el-button class="player-btn" type="infor" size="mini" @click="handlePlayOrPause">
          <i class="fa"
              :class="`fa-${isPlaying ? 'pause' : 'play'}`"
              aria-hidden="true">
          </i>
        </el-button>
        <el-button class="player-btn" type="infor" size="mini" @click="handleStop">
          <i class="fa fa-stop" aria-hidden="true"></i>
        </el-button>
        <el-slider v-model="playerSpeed"
                  class="slider"
                  :show-tooltip="false"
                  @change="handleChangeOfSlider"
                  >
        </el-slider>
        <div title="是否显示轨迹点">
          <svg-icon class="fz-22 ve-m m-r-5" icon-class="trackPoint"/>
          <el-switch
            v-model="showTrackPoint">
          </el-switch>
        </div>
      </div>
    </el-popover>
    <div class="camera">
      <el-button ref="control" size="small" v-popover:trackBtn><svg-icon icon-class="camera"/></el-button>
      <el-button @click="showDetailData = !showDetailData" size="small" icon="el-icon-menu" :class="{ 'c-blue': showDetailData }"></el-button>
    </div>
    <div v-show="showDetailData" class="content-wrapper">
      <!-- <section>
        <h2>统计</h2>
        <table>
          <tr>
            <td>行驶时长</td>
            <td><span>00:11</span> h</td>
            <td>行驶里程</td>
            <td><span>5.58</span> km</td>
          </tr>
          <tr>
            <td>行驶油耗</td>
            <td><span>0.6</span> L</td>
            <td>平均速度</td>
            <td><span>19.5</span> km/h</td>
          </tr>
          <tr>
            <td>平均油耗</td>
            <td><span>17.1</span> L/100km</td>
            <td>轨迹来源</td>
            <td><span>MFS</span></td>
          </tr>
          <tr>
            <td>轨迹点</td>
            <td><span>69</span> 个</td>
            <td></td>
            <td></td>
          </tr>
        </table>
      </section>
      <section>
        <h2>速度</h2>
      </section> -->
      <el-tabs tab-position="left" style="height: 300px;" class="statDataTabs">
        <el-tab-pane :label="`行程(${tripList.length})`">
          <ul class="tripList">
            <li v-for="(i, index) in tripList" :key="index" @click="onTripClick(i.pointArr, index)" :class="{ 'is-active': i.isActive }">
              <h2>{{i.startTime}} 行程记录</h2>
              <p class="startPoint"><i class="el-icon-location c-blue"></i>{{i.startTime.substring(11, 16)}} 从: {{i.startAddress}}</p>
              <p class="endPoint"><i class="el-icon-location c-red"></i>{{i.endTime.substring(11, 16)}} 到: {{i.endAddress}}</p>
              <p class="statData">
                行驶耗时 <span class="number">{{i.travelTime}}</span>
                <br>
                行驶里程 <span class="number">{{(i.travelMil/1000).toFixed(1)}} km</span>
              </p>
              <p class="stayTime">停留时间: {{i.packTime}}</p>
            </li>
          </ul>
        </el-tab-pane>
        <el-tab-pane :label="`停留(${stayMarkers.length})`">
          <ul class="tripList">
            <li v-for="(i, index) in stayMarkers" :key="index" @click="onStayListClick(index)" :class="{ 'is-active': i.isActive }">
              <h2>{{i.gpstime}} 停留记录</h2>
              <p class="startPoint"><i class="el-icon-location c-blue"></i>{{i.address}}</p>
              <p class="stayTime">停留时间: {{i.packTime}}</p>
            </li>
          </ul>
        </el-tab-pane>
        <el-tab-pane :label="`报警(${alertList.length})`">
          <ul class="tripList">
            <li v-for="(i, index) in alertList" :key="index" @click="onAlertListClick(index)" :class="{ 'is-active': i.isActive }">
              <h2 class="c-blue">{{i.alerttime}} 报警记录</h2>
              <p class="startPoint"><a class="f-w-b">设备名称:</a> {{i.devicename}}</p>
              <p class="startPoint"><a class="f-w-b">告警类型:</a> {{i.typename}}</p>
              <p class="startPoint"><a class="f-w-b">告警地点:</a> {{i.address}}</p>
            </li>
          </ul>
        </el-tab-pane>
        <el-tab-pane label="围栏(0)"></el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script>
import $ from 'jquery';
import 'static/jq-resizable/jquery-ui.min';
import 'static/jq-resizable/jquery-ui.structure.min.css';
import Vue from 'vue';

import http from '@/assets/js/http';
import mapMixin from '@/assets/js/map_com';

import { locationTypeMap } from '@/assets/js/config';

const orientation = ['北', '东北', '东', '东南', '南', '西南', '西', '西北'];

export default {
  mixins: [
    http,
    mapMixin,
  ],
  data() {
    return {
      alertList: [],
      showDetailData: false,
      tripList: [],
      monitorBottomShrink: false,
      transformLngLatAllTimer: null,
      defaultValue: moment().add(-1, 'month'),
      tableData: [],
      polygons: [],
      polyline: {
        editable: false,
        strokeColor: '#FF4111',
        strokeWeight: 3,
        path: [],
      },
      circles: [],
      windows: [],
      markers: [],
      stayMarkers: [],
      infoMarker: {
        id: null,
        position: [],
        content: '',
      },
      // alertMarker: {
      //   id: null,
      //   position: [],
      // },
      params: {
        sn: null,
        coord: 'baidu',
        sTime: null,
        eTime: null,
        locationType: '',
      },
      playerSpeed: 50,
      isLoading: false,
      transformIndex: 0,
      transformAllStatus: false,
      pathRenderOptions: {
        // 轨迹线的样式
        pathLineStyle: {
          strokeStyle: 'green',
          lineWidth: 6,
          dirArrowStyle: true,
        },
      },
    };
  },
  computed: {
    $map() {
      return this.$refs.amap.$$getInstance();
    },
    speed() {
      return this.playerSpeed * 100;
    },
    showTrackPoint: {
      get() {
        if (this.pathSimplifierIns) {
          const { renderAllPointsIfNumberBelow } = this.pathSimplifierIns.getRenderOptions();
          return renderAllPointsIfNumberBelow !== -1;
        }
        return false;
      },
      set(val) {
        if (this.pathSimplifierIns) {
          const options = this.pathSimplifierIns.getRenderOptions();
          options.renderAllPointsIfNumberBelow = val ? 50000 : -1;
          this.pathSimplifierIns.renderLater();
        }
      },
    },
  },
  mounted() {
    const el = this.$refs.control.$el;
    if (el) el.click();
    $('#trackBottom').resizable({
      handles: 'n',
      minHeight: 150,
      helper: 'ui-resizable-helper-top',
      stop: (event, { element, size }) => {
        element[0].style.height = `${size.height}px`;
      },
    });
  },
  created() {
    const { isPassive } = this.$route.query;
    if (isPassive === '0') {
      this.pathRenderOptions.pathLineStyle.lineWidth = 3;
      this.pathRenderOptions.pathLineStyle.dirArrowStyle = false;
      this.pathRenderOptions.pathLineSelectedStyle.lineWidth = 3;
      this.pathRenderOptions.pathLineSelectedStyle.dirArrowStyle = false;
    }
  },
  methods: {
    clearData() {
      this.markers = [];
      this.stayMarkers = [];
      this.windows = [];
      this.tripList = [];
      this.polyline.path = [];
    },
    init() {
      this.pathSimplifierIns.on('pointClick', (e, { pointIndex }) => {
        this.setPointInfoWindow(pointIndex);
        this.$refs.table.setCurrentRow(this.tableData[this._tableDataLength - pointIndex - 1]);
        this.$nextTick(() => {
          const wrapper = this.$refs.table.$el.querySelector('.el-table__body-wrapper');
          wrapper.scrollTop = wrapper.querySelector('.current-row').offsetTop;
        });
      });
      const { sn, gpstime, carnumber } = this.$route.query;
      this.params.sn = sn;
      this.params.carnumber = carnumber;
      // this.time = [moment(gpstime).startOf('day'), gpstime];
      this.params.sTime = moment(gpstime).startOf('day').format('YYYY-MM-DD HH:mm:ss');
      this.params.eTime = moment(gpstime).endOf('day').format('YYYY-MM-DD HH:mm:ss');
      this.showTrack();
    },
    handleRowClick({ _index }) {
      this.setPointInfoWindow(_index);
    },
    setPointInfoWindow(pointIndex) {
      const { gpsinfo:
              { lng, lat, gpstime, state },
              locationMode,
              directionText,
              address,
            } = this.tableData[this._tableDataLength - pointIndex - 1];
      const { carnumber, devicename } = this.$route.query;
      const stayPointIndex = this.stayMarkers.findIndex(i => i.gpstime === gpstime);
      const isStayPoint = stayPointIndex !== -1;
      this.windows = [{
        id: Math.random().toString().replace('.', ''),
        position: [lng, lat],
        template: `
          <section class="info-windows mini">
            <h1><a>${carnumber || devicename}</a> <a>${isStayPoint ? '<span class="c-red f-w-b">停留点</span>' : ''}  [${pointIndex + 1}/${this.tableData.length}]</a></h1>
            ${isStayPoint ? `<p class="c-red f-w-b"><a>停留时间:</a> <a>${this.stayMarkers[stayPointIndex].packTime}</a></p>` : ''}
            <p><a>经度:</a> <a>${lng} <span class=" m-l-10 f-w-b">定位: ${locationMode}</span></a></p>
            <p><a>纬度:</a> <a>${lat} <span class=" m-l-10 f-w-b">航向: ${directionText}</span></a></p>
            <p><a>时间:</a> <a>${gpstime}</a></p>
            <p><a>状态:</a> <a>${state}</a></p>
            <p><a>位置:</a> <a>${address}</a></p>
          </section>
        `,
        visible: true,
      }];
    },
    getHoverTitle(pathData, pathIndex, pointIndex) {
      // 返回鼠标悬停时显示的信息
      if (pointIndex >= 0) {
        // 鼠标悬停在某个轨迹节点上
        const {
          address,
          gpsinfo: { lng, lat, gpstime },
        } = this.tableData[this._tableDataLength - pointIndex - 1];
        return `
          ${pathData.name}，[点:${pointIndex + 1}/${pathData.path.length}]<br>
          经度: ${lng}<br>
          纬度: ${lat}<br>
          定位时间: ${gpstime}<br>
          位置: ${address}
        `;
      }
      // 鼠标悬停在节点之间的连线上
      return `${pathData.name}，点数量${pathData.path.length}`;
    },
    afterHandleStop() {
      this.infoMarker.position = this.path[0].path[0];
    },
    handleChangeOfSlider() {
      if (this.navig) {
        this.navig.setSpeed(this.speed);
      }
    },
    async getAlertList() {
      const { sn, sTime, eTime } = this.params;
      const { data } = await this.apiPost('/deviceAlert/getDeviceAlertList', { timeType: 7, begintime: sTime, endtime: eTime, sn });
      Promise.all(data.map((i, index) => this.getAddress([i.lng, i.lat])
        .then(({ regeocode: { formattedAddress } }) => {
          data[index].address = formattedAddress;
        }))).then(() => {
          this.alertList = data;
        });
    },
    async showTrack() {
      const { sn, carnumber, devicename, dateMil, totalMil } = this.$route.query;
      this.isLoading = true;
      try {
        const { data: dataList } = await this.apiPost('/device/findTrackHis', this.params);
        const { data: stayDataList } = await this.apiPost('/hisTrack/getTravelEvent', this.params);
        this.getAlertList();
        if (!dataList.length) {
          this.$message.info('查无历史轨迹');
          this.isLoading = false;
          return;
        }
        this.clearData();

        const self = this;

        const { gpsinfo: { lng, lat, speed, direction, gpstime } } = dataList[0];
        this.infoMarker = {
          id: Math.random().toString().replace('.', ''),
          position: [lng, lat],
          visible: true,
          size: new AMap.Size(170, 165),
          content: new Vue({
            data() {
              return {
                lng,
                lat,
                speed,
                direction,
                gpstime,
                // address: null,
              };
            },
            created() {
              self.getAddress([lng, lat]).then(({ regeocode: { formattedAddress } }) => {
                if (formattedAddress) this.address = formattedAddress;
              });
              this.$bus.on(
                'trackReplayMove',
                ({ lng: _lng,
                  lat: _lat,
                  speed: _speed,
                  direction: _direction, gpstime: _gpstime }) => {
                  this.lng = _lng;
                  this.lat = _lat;
                  this.speed = _speed;
                  this.direction = _direction;
                  this.gpstime = _gpstime;
                  // this.address = _address;
                },
              );
            },
            destroyed() {
              this.$bus.off('trackReplayMove');
            },
            computed: {
              computedCourse() {
                return self.handleDirection(this.direction);
              },
            },
            render() {
              return (
                <section class="bubble-window">
                  <div class="arrow">
                    <em></em><span></span>
                  </div>
                  <h1>
                    <a title={carnumber || devicename}>{devicename}</a> <a />
                  </h1>
                  <p>
                    <a>IMEI:</a> <a>{sn}</a>
                  </p>
                  <p>
                    <a>经度:</a> <a>{this.lng}</a>
                  </p>
                  <p>
                    <a>纬度:</a> <a>{this.lat}</a>
                  </p>
                  <p>
                    <a>速度:</a> <a>{this.speed} km/h</a>
                    <a class="m-l-15">航向:</a> <a>{this.computedCourse}</a>
                  </p>
                  <p>
                    <a>时间:</a> <a>{this.gpstime}</a>
                  </p>
                </section>
              );
            },
          }).$mount(document.createElement('div')).$el,
          template: '',
        };
        const path = dataList.map((i, index) => {
          const { gpsinfo: { direction: _direction, locationType } } = i;
          dataList[index]._index = index;
          dataList[index].devicename = devicename;
          dataList[index].dateMil = dateMil === 'null' ? '' : dateMil;
          dataList[index].totalMil = totalMil === 'null' ? '' : totalMil;
          dataList[index].isTransform = false;
          dataList[index].address = '';
          dataList[index].directionText = this.handleDirection(_direction);
          dataList[index].locationMode = locationType === '' ? '无定位' : locationTypeMap[locationType];
          return [i.gpsinfo.lng, i.gpsinfo.lat];
        });
        stayDataList.forEach((i, index) => {
          const startPointIndex = dataList.findIndex(_i => _i.gpsinfo.gpstime === i.starttime);
          const endPointIndex = dataList.findIndex(_i => _i.gpsinfo.gpstime === i.stoptime);
          let packTime = null;
          if (i.stay !== '起步前') {
            packTime = this.$secondsFormat(i.stay / 1000, false);
            const prev = stayDataList[index - 1];
            const pointIndex = dataList.findIndex(_i => _i.gpsinfo.gpstime === prev.stoptime);
            const _index = this.stayMarkers.push({
              name: 'other',
              pointIndex,
              packTime,
              address: null,
              gpstime: prev.stoptime,
              title: `时间: ${prev.stoptime} ,停车时间: ${packTime}`,
              position: [prev.endLng, prev.endLat],
              angle: 0,
              offset: [-16, -36],
              template: '<img src="/static/images/stop-m.png"/>',
              animation: 'AMAP_ANIMATION_DROP',
            });
            const current = this.stayMarkers[_index - 1];
            this.getAddress(current.position).then(({ regeocode: { formattedAddress } }) => {
              current.address = formattedAddress;
            });
          } else {
            packTime = i.stay;
          }
          const pointArr = dataList.slice(startPointIndex, endPointIndex + 1);
          this.setTripList(
            dataList[startPointIndex],
            dataList[endPointIndex],
            packTime,
            pointArr,
            i.travelMil);
        });
        this.tableData = _.reverse([...dataList]);// 表格倒序显示
        this._tableDataLength = this.tableData.length;
        this.transformIndex = 0;
        this.transformAllStatus = false;
        this.path = [
          {
            name: '轨迹',
            path,
            _dataList: dataList,
          },
        ];
        this.start();
      } finally {
        this.isLoading = false;
      }
    },
    setTripList(startPoint, endPoint, packTime, pointArr, travelMil) {
      const index = this.tripList.push({
        pointArr,
        packTime,
        travelMil,
        isActive: false,
        startAddress: null,
        endAddress: null,
        startTime: startPoint.gpsinfo.gpstime,
        endTime: endPoint.gpsinfo.gpstime,
        startPoint: [startPoint.gpsinfo.lng, startPoint.gpsinfo.lat],
        endPoint: [endPoint.gpsinfo.lng, endPoint.gpsinfo.lat],
        travelTime: this.$secondsFormat(
                      moment(endPoint.gpsinfo.gpstime)
                        .diff(moment(startPoint.gpsinfo.gpstime), 'second'), false),
      });
      const current = this.tripList[index - 1];
      this.getAddress(current.startPoint).then(({ regeocode: { formattedAddress } }) => {
        current.startAddress = formattedAddress;
      });
      this.getAddress(current.endPoint).then(({ regeocode: { formattedAddress } }) => {
        current.endAddress = formattedAddress;
      });
    },
    onAlertListClick(index) {
      const { lng, lat, alerttime, typename, devicename, address } = this.alertList[index];
      // this.alertMarker.id = Math.random().toString().replace('.', '');
      // this.alertMarker.position = [lng, lat];
      this.$map.setCenter([lng, lat]);
      this.windows = [{
        id: Math.random().toString().replace('.', ''),
        position: [lng, lat],
        template: `
          <section class="info-windows mini">
            <h1><a>${devicename}</a> <a class="c-red f-w-b">告警点</a></h1>
            <p><a>告警类型:</a> <a>${typename}</a></p>
            <p><a>告警时间:</a> <a>${alerttime}</a></p>
            <p><a>告警地点:</a> <a>${address}</a></p>
          </section>
        `,
        visible: true,
      }];
    },
    onTripClick(pointArr, index) {
      this.tripList.forEach((i) => {
        i.isActive = false;
      });
      this.tripList[index].isActive = true;
      this.polyline.path = pointArr.map(i => [i.gpsinfo.lng, i.gpsinfo.lat]);
      this.$nextTick(() => {
        this.$map.setFitView(this.$map.getAllOverlays('polyline'));
      });
    },
    onStayListClick(index) {
      this.stayMarkers.forEach((i) => {
        i.isActive = false;
      });
      this.stayMarkers[index].isActive = true;
      this.setPointInfoWindow(this.stayMarkers[index].pointIndex);
    },
    onNavigatorMove() {
      const { idx } = this.navig.getCursor();
      const position = this.navig.getPosition();
      this.infoMarker.position = [position.lng, position.lat];
      if (idx === this._lastIdx) {
        return;
      }
      const map = this.$map;
      this._lastIdx = idx;
      const [{ _dataList }] = this.path;
      const { gpsinfo: { lng, lat, gpstime, speed, direction } } = _dataList[idx];
      if (!map.getBounds().contains([position.lng, position.lat])) {
        map.setCenter([lng, lat]); // 判断车辆超出视图范围才定位到地图中心
      }
      this.$bus.emit('trackReplayMove', { lng, lat, gpstime, speed, direction });
    },
    async transformLngLat(index) {
      const { isTransform, gpsinfo: { lng, lat } } = this.tableData[index];
      this.tableData[index].isTransform = !isTransform;
      try {
        const { regeocode: { formattedAddress } } = await this.getAddress([lng, lat]);
        this.tableData[index].address = formattedAddress;
      } catch (e) {
        this.tableData[index].address = '解析失败';
      }
    },
    transformLngLatAll() {
      if (this.transformAllStatus) {
        this.transformAllStatus = false;
        window.clearTimeout(this.transformLngLatAllTimer);
        this.transformLngLatAllTimer = null;
      } else {
        this.transformAllStatus = true;
        this.setTransformLngLatAllTimer();
      }
    },
    async setTransformLngLatAllTimer() {
      await this.transformLngLat(this.transformIndex);
      this.transformLngLatAllTimer = setTimeout(() => {
        if (!this.transformAllStatus || this.transformIndex === this.tableData.length) return;
        this.transformIndex += 1;
        this.setTransformLngLatAllTimer();
      });
    },
    formatJson(filterVal, jsonData) {
      return jsonData.map((v, index) => filterVal.map((j) => {
        if (j === 'id') {
          return index + 1;
        }
        return _.get(v, j);
      }));
    },
    exportTrackToExcel() {
      require.ensure([], () => {
        const { export_json_to_excel } = require('vendor/Export2Excel');
        const tHeader = ['序号', '定位方式', '经度', '纬度', '地址', '速度(km/h)', '方向', '状态', '定位时间'];
        const filterVal = ['id', 'locationMode', 'gpsinfo.lng', 'gpsinfo.lat', 'address', 'gpsinfo.speed', 'directionText', 'gpsinfo.state', 'gpsinfo.gpstime'];
        const list = this.tableData;
        const data = this.formatJson(filterVal, list);
        export_json_to_excel(tHeader, data, `轨迹报表_${this.params.carnumber}`);
        // this.downloadLoading = false
      });
    },
    positionRenderHeader(h, { column: { label } }) {
      return (
        <span>
          <span>{label}</span>
          &nbsp;&nbsp;
          {this.transformIndex === this.tableData.length ? '' :
            <el-button on-click={this.transformLngLatAll}
              size="mini" type="text" icon="el-icon-location-outline">{this.transformAllStatus === false ? '一键解析' : '暂停'}</el-button>
          }
          <el-button on-click={this.exportTrackToExcel} size="mini" type="text" icon="el-icon-download">导出</el-button>
        </span>
      );
    },
    handleDirection(direction) {
      let ot = '北';
      if (direction && direction > 0) {
        let idx = Math.ceil(direction / 45);
        idx = idx === 8 ? 0 : idx - 1;
        ot = orientation[idx];
      }
      return ot;
    },
  },
  components: {
  },
};
</script>

<style>
  .bubble-window{ padding: 5px;width:200px; height:100px; border:1px solid #409EFF; position:relative; background-color:#FFF; border-radius: 5px; color: #606266; font-size: 14px }
  .bubble-window>.arrow{ position:absolute; width:20px; height:40px; bottom:-40px; left:95px; }
  .bubble-window>.arrow *{ display:block; border-width:10px; position:absolute; border-style:solid dashed dashed dashed; font-size:0; line-height:0; }
  .bubble-window>.arrow em{border-color:#409EFF transparent transparent;}
  .bubble-window>.arrow span{border-color:#FFF transparent transparent; top:-1px;}
  .bubble-window p { line-height: 16px; }
  .bubble-window h1 a { display: block; text-overflow: ellipsis; word-break: keep-all; overflow: hidden;font-weight: bold;line-height: 20px; }
  .statDataTabs .el-tabs__content {height: 100%; overflow-y: auto}
  .statDataTabs .el-tabs__item {padding: 0 15px}
</style>


<style lang="stylus" scoped>
  .trackReplay-wrapper
    display: flex
    flex-direction: column
    width: 100%
    height: 100%
    position: relative
    .loading
      position: absolute
      z-index: 3000
      top: 30px
      left: 50%
      padding: 5px 0px
      margin-left: -100px
      width: 150px
      height: 30px
      line-height: 30px
      border-radius: 7px
      border: 1px solid #ddd
      background: rgba(255, 255, 255, .9)
      text-align: center
      color: #20A0FF
      .text
        font-size: 14px
    .map
      height: 0
      width: 100%
      flex: 1 1 auto
    .table
      position: relative
      flex: 0 0 auto
      width: 100%
      #trackBottom
        top: 0px !important
    .camera
      display: flex
      flex-direction column
      position: absolute
      top: 15px
      right: 15px
      z-index: 200
      .el-button
        margin-left 0px
        margin-bottom 5px

  .player-wrapper
    display: flex
    padding: 5px
    border-radius: 5px
    background: rgba(255, 255, 255, 0.85)
    line-height: 36px
    align-items: center
    flex-wrap: wrap
    >button.player-btn
      width: 30px
      height: 30px
      padding: 8px 9px
      border-radius: 50%
      .fa-play
        margin-left: 2px
    .slider
      flex: 1 1 auto
      padding: 0px 15px 0px 15px
    .progress
      flex: 1 1 100%
      padding: 10px 0px 5px
    .locationRatio
      flex: 1 1 auto
      text-align: center
      >.el-radio+.el-radio
        margin-left: 10px
        >.el-radio__label
          padding-left: 5px

  .content-wrapper
    position absolute
    top 150px
    right 15px
    width 380px
    padding 20px 5px
    background #fff
    border-radius 3px
    border 1px solid #409EFF
    .tripList
      height 100%
      margin-right 10px
      overflow-y auto
      font-size 14px
      li
        margin-bottom 10px
        padding 5px 5px 0px
        transition background-color .25s ease
        cursor pointer
        border-radius 5px
        &:hover
          background-color #f5f7fa
        &.is-active
          background-color #d1e6ff
        h2
          padding-bottom 5px
          margin-bottom 5px
          border-bottom 1px solid
          font-weight bold
        p.startPoint
          margin-bottom 5px
        p.startPoint,
        p.endPoint
          line-height 18px
        p.statData
          margin 7px 0px
          padding 3px 5px
          line-height 18px
          border-radius 5px
          border 1px dashed #ccc
          .number
            color #409EFF
        p.stayTime
          padding-bottom 7px
          color #409EFF
          border-bottom 1px dashed
    section
      position relative
      margin-top 20px
      padding-top 10px
      padding-bottom 5px
      border 1px solid #409EFF
      &:first-child
        margin-top 0px
      h2
        position absolute
        top -10px
        left 5px
        padding 0px 3px
        background #fff
        color #409EFF
      table
        width 100%
        margin 0px 5px
        font-size 14px
        line-height 20px
        tr
          td
            margin 10px 0px
            span
              color #409EFF
              font-weight bold
</style>
