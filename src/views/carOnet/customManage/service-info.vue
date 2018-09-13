<template>
    <el-dialog
    title="服务记录"
    :visible.sync="centerDialogVisible"
    width="30%"
    :modal-append-to-body="false"
    center>
    <ul class="custom-timeline">
      <li class="item">
        <div class="item-tail"></div>
        <div class="item-head blue"></div>
        <span class="hhmmss">时间</span>
        <div class="item-content">
          <p class="time">方案</p>
          <p class="title"> 门店</p>
          <p class="content"> 时间</p>
        </div>
      </li>
    </ul>
    <div class="pos-rel p-t-20 pages-footer">
      <div class="stat">
      </div>
      <div class="block pages m-t-15">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          layout="total, sizes, prev, pager, next, jumper"
          :page-sizes="[15, 30, 60, 100]"
          :page-size="pagination.pageSize"
          :current-page="pagination.curPage"
          :total="pagination.dataCount">
        </el-pagination>
      </div>
    </div>
    <span slot="footer" class="dialog-footer">          
        <el-button @click="centerDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="centerDialogVisible = false">确 定</el-button>
    </span>
    </el-dialog>
</template>

<script>
import http from '@/assets/js/http';
import listMixin from '@/assets/js/list_com';

export default {
  mixins: [http, listMixin],
  data() {
    return {
      centerDialogVisible: false,
      tabBtn: 'movementInfo',
      userInput: null,
      tabPosition: 'left',
      pagination: {
        pageSize: 10,
        fatherid: null,
        currentPage: 1,
        loginId: Lockr.get('securId'),
      },
    };
  },
  methods: {
    async open() {
      this.centerDialogVisible = true;
    },
  },
};
</script>

<style lang="stylus" scoped rel="stylesheet/stylus">
.custom-timeline
    list-style: none;
    margin: 0;
    padding: 20px 0 0 20px;
    .item
      margin: 0!important;
      padding: 0 0 12px;
      list-style: none;
      position: relative;
      cursor pointer
      &:hover
        text-decoration underline
        // background: rgba(0, 0, 0, 0.1)
      &:last-child
        .item-tail
          display: none;
      .item-tail
        height: 100%;
        border-left: 1px solid #e9eaec;
        position: absolute;
        left: 48px;
        top: 0;
      .item-head
        width: 15px;
        height: 15px;
        background-color: #fff;
        border-radius: 50%;
        border: 1px solid transparent;
        position: absolute;
        left: 40px
        &.blue
          border-color: #2d8cf0;
          color: #2d8cf0;
      .hhmmss
        position absolute
        top: 50%
        transform translateY(-50%)
        font-size: 16px
        font-weight bold
      .item-content
        padding: 1px 1px 10px 64px;
        font-size: 12px;
        position: relative;
        top: -3px;
        .time
          font-size: 15px;
          font-weight: 700;
          margin: 0;
        .content, .title
          padding-left: 5px;
        .title
          color: #F56C6C
</style>




