<template>
  <div class="wrap1">
    <div class="main1">
      <tab
        class="tab fix_tab"
        :line-width="0"
        bar-active-color="#f0b90b"
        active-color="#f0b90b"
        :scroll-threshold="5"
        v-model="type"
      >
        <tab-item selected @click.native="navTap(0)">我的买入</tab-item>
        <tab-item @click.native="navTap(1)">我的卖出</tab-item>
        <!-- <tab-item @click.native="navTap(2)">买入进度</tab-item>
        <tab-item @click.native="navTap(3)">卖出进度</tab-item>-->
      </tab>
      <div class="scroll_div">
        <van-pull-refresh
          v-model="isLoading"
          :pulling-text="$t('more.a')"
          :loosing-text="$t('more.b')"
          :loading-text="$t('more.c')"
          @refresh="onRefresh"
        >
          <div
            class="div"
            v-infinite-scroll="loadMore"
            infinite-scroll-disabled="loading"
            infinite-scroll-distance="10"
            infinite-scroll-immediate-check="false"
          >
            <x-table v-if="type==0||type==1" :content-bordered="false" :cell-bordered="false">
              <thead>
                <tr>
                  <th>{{$t('trade.child2.list.t1')}}</th>
                  <th>{{$t('trade.child2.list.t2')}}</th>
                  <th>{{$t('trade.child2.list.t3')}}（{{$store.state.active_market.name}}）</th>
                  <th>{{$t('trade.child2.list.t4')}}</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item,index) in list" :key="index">
                  <td>
                    <span>{{item.time1}}</span>
                    <span>{{item.time2}}</span>
                  </td>
                  <td>{{item.price}}</td>
                  <td>{{item.number}}</td>
                  <td @click="cancel(item.id)">{{$t('trade.child2.btn.b1')}}</td>
                </tr>
              </tbody>
            </x-table>
            <x-table v-if="type==2||type==3" :content-bordered="false" :cell-bordered="false">
              <thead>
                <tr>
                  <th>订单编号</th>
                  <th>总额(USD≈CNY)</th>
                  <th>交易时间</th>
                  <th>操作</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td>1330000000002</td>
                  <td>
                    <span>1200.00</span>
                    <span>≈100.00</span>
                  </td>
                  <td>3:00:00</td>
                  <td>
                    <button class="btn" @click="toPay">去支付</button>
                  </td>
                </tr>
                <tr>
                  <td>1330000000002</td>
                  <td>
                    <span>1200.00</span>
                    <span>≈100.00</span>
                  </td>
                  <td>3:00:00</td>
                  <td>
                    <span>已支付</span>
                    <span>等待确认</span>
                  </td>
                </tr>
                <tr>
                  <td>1330000000002</td>
                  <td>
                    <span>1200.00</span>
                    <span>≈100.00</span>
                  </td>
                  <td>3:00:00</td>
                  <td>
                    <button class="btn" @click="confirmReceive">已收款</button>
                  </td>
                </tr>
                <tr>
                  <td>1330000000002</td>
                  <td>
                    <span>1200.00</span>
                    <span>≈100.00</span>
                  </td>
                  <td>3:00:00</td>
                  <td>
                    <span>等待收款</span>
                  </td>
                </tr>
              </tbody>
            </x-table>
          </div>
          <load-more v-if="lif" :show-loading="load" :tip="$t('more.c')"></load-more>
          <load-more v-if="nif" :show-loading="none" :tip="$t('more.d')"></load-more>
        </van-pull-refresh>
      </div>
    </div>
  </div>
</template>
<script>
import $ from "jquery";
import { PullRefresh } from "vant";
import { Tab, TabItem, XTable, LoadMore } from "vux";

export default {
  data() {
    return {
      type: 0,
      list: [],
      page: 1,
      load: true,
      none: false,
      lif: true,
      nif: false,
      loading: false,
      isLoading: false
    };
  },
  components: {
    Tab,
    TabItem,
    XTable,
    LoadMore,
    "van-pull-refresh": PullRefresh
  },
  mounted() {
    let that = this;
    mui.back = function() {
      mui.plusReady(function() {
        var main = plus.android.runtimeMainActivity();
        main.moveTaskToBack(false);
      });
      error;
    };
    this.$nextTick(() => {
      if (this.$route.params.type) {
        this.type = Number(this.$route.params.type);
      } else {
        this.type = 0;
      }
      that.getlist(1);
    });
  },
  methods: {
    navTap(i) {
      this.type = i;
      let that = this;
      that.loading = false;
      that.nif = false;
      that.page = 1;
      that.list = [];
      that.getlist(1);
    },
    cancel(id) {
      let that = this;
      that.$vux.confirm.show({
        title: that.$t('confirm.card.title.t5'),
        confirmText: that.$t('cfm'),
        cancelText: that.$t('cel'),
        content:
          "<div style='text-align:left'>"+that.$t('confirm.card.con.c5')+"</div>",
        onShow() {},
        onHide() {},
        onCancel() {},
        onConfirm() {
          that.$vux.loading.show({
            text: ""
          });
          that
            .$http({
              url: "/Transaction/max_sell_tips",
              method: "post",
              data: {
                token: that.$store.state.user_info.token,
                trade_id: id
              }
            })
            .then(function(res) {
              that.$vux.loading.hide();
              if (res.data.code == 1) {
                that.$vux.toast.show({
                  text: res.data.msg,
                  type: "success",
                  position: "middle",
                  time: 1200
                });
                that.getlist(1);
              } else {
                that.$vux.toast.show({
                  text: res.data.msg,
                  type: "cancel",
                  position: "middle",
                  time: 1200
                });
              }
            });
        }
      });
    },
    toPay() {
      this.$router.push({
        name: "topay"
      });
    },
    confirmReceive() {
      let that = this;
      that.$vux.confirm.show({
        title: "放币",
        confirmText: "确认",
        cancelText: "取消",
        content: "确认收到对方款项，现在放币？",
        onShow() {},
        onHide() {},
        onCancel() {},
        onConfirm() {}
      });
    },
    onRefresh() {
      let that = this;
      that.isLoading = true;
      that.loading = false;
      that.nif = false;
      that.page = 1;
      that.list = [];
      that.getlist(0);
    },
    loadMore() {
      let that = this;
      that.lif = true;
      that.page++;
      that.getlist();
    },
    getlist(i) {
      let that = this;
      if (i) {
        that.lif = true;
      }
      let tt;
      if (that.type == 0) {
        tt = 2;
      } else if (that.type == 1) {
        tt = 1;
      } else if (that.type == 2) {
        tt = 4;
      } else {
        tt = 3;
      }
      that
        .$http({
          url: "/Transaction/trade_list",
          method: "post",
          data: {
            token: that.$store.state.user_info.token,
            cur_id: that.$store.state.active_market.id,
            page: that.page,
            type: tt
          }
        })
        .then(function(res) {
          that.lif = false;
          that.isLoading = false;
          if (res.data.code == 1) {
            if (res.data.data.list.length) {
              for (let v of res.data.data.list) {
                v.time1 = v.start_date.toString().split(" ")[0];
                v.time2 = v.start_date.toString().split(" ")[1];
              }
              that.list = that.list.concat(res.data.data.list);
            } else {
              that.nif = true;
              that.loading = true;
            }
          } else {
            that.$vux.toast.show({
              text: res.data.msg,
              type: "text",
              position: "middle",
              time: 1200
            });
          }
        });
    }
  }
};
</script>
<style lang="less" scoped>
.wrap1 {
  font-size: 0.36rem;
  width: 100%;
  height: 100%;
  color: white;
  box-sizing: border-box;
  overflow: auto;
  .top {
    background: #1a212a;
  }
  .main1 {
    height: 100%;
    padding-top: 44px;
    box-sizing: border-box;
    .tab {
      z-index: 999;
      position: fixed;
      width: 100%;
      top: 1.3rem;
      left: 0;
      > div {
        background: #161d26;
      }
    }
    .scroll_div {
      width: 100%;
      height: 100%;
    }
  }

  table {
    background: #1a212a;
  }
  thead {
    // background: #161d26;
    tr {
      th {
        text-align: center;
        color: #8f8f9b;
        // padding: 0.3rem 0.2rem 0.3rem;
        font-size: 0.32rem;
        border-bottom: 1px solid #2a333e;
      }
      th:last-child {
        padding: 0.3rem 0.2rem 0.3rem 0;
      }
    }
  }
  tbody {
    tr {
      td {
        text-align: center;
        color: #8f8f9b;
        border-bottom: 1px solid #2a333e;
        // padding: 0.3rem 0.2rem 0.3rem;
        font-size: 0.32rem;
        > span {
          display: block;
        }
        > span:first-child {
          padding-bottom: 0.1rem;
        }
        .btn {
          border-radius: 4px;
          color: white;
          line-height: 0.66rem;
          padding: 0 0.3rem 0;
          background: #f0b90b;
          border-radius: 20px;
        }
      }
      td:last-child {
        padding: 0.3rem 0.2rem 0.3rem 0;
      }
    }
    > tr:last-child {
      td {
        border-bottom: 0;
      }
    }
  }
}
</style>
