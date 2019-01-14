<template>
  <div class="wrap">
    <div class="top">
      <img @click="back" class="back_img" src="../assets/nav_back.png" alt>
      <div>{{$t('topay.title')}}</div>
    </div>
    <div class="main">
      <div v-if="info.paypal" class="ul_title">{{$t('topay.t1')}}</div>
      <ul v-if="info.paypal">
        <li>
          <span>Paypal：</span>
          <span>{{info.paypal}}</span>
        </li>
      </ul>
      <div v-if="info.bank_card" class="ul_title">{{$t('topay.t2')}}</div>
      <ul v-if="info.bank_card">
        <li>
          <span>{{$t('topay.list1.l1')}}：</span>
          <span>{{info.bank_card}}</span>
        </li>
        <li>
          <span>{{$t('topay.list1.l2')}}：</span>
          <span>{{info.bank_text}}</span>
        </li>
        <li>
          <span>{{$t('topay.list1.l3')}}：</span>
          <span>{{info.open_name}}</span>
        </li>
        <li>
          <span>{{$t('topay.list1.l4')}}：</span>
          <span>{{info.bank_add}}</span>
        </li>
      </ul>
      <div class="ul_title">{{$t('topay.t3')}}</div>
      <ul>
        <li>
          <span>{{$t('topay.list2.l1')}}：</span>
          <span>{{info.alipay_account}}</span>
        </li>
        <li>
          <span>{{$t('topay.list2.l2')}}：</span>
          <span>{{info.alipay_name}}</span>
        </li>
      </ul>
      <div class="ul_title">{{$t('topay.t4')}}</div>
      <ul>
        <li>
          <span>{{$t('topay.list3.l1')}}：</span>
          <span>{{info.wechat_nick}}</span>
        </li>
        <!-- <li>
          <span>{{$t('topay.list3.l2')}}：</span>
          <span>
            <img src="../assets/logo.png" alt @click="showImg">
          </span>
        </li>-->
      </ul>
      <div class="d_btn">
        <button class="btn" @click="sub">{{$t('topay.tip.t1')}}</button>
      </div>
    </div>
    <div class="mask" v-if="big_if" @click="big_if = false">
      <div>
        <img :src="src" alt>
      </div>
    </div>
  </div>
</template>
<script>
import $ from "jquery";
import src from "../assets/logo.png";
export default {
  data() {
    return {
      big_if: false,
      src: src,
      info: {}
    };
  },
  components: {},
  mounted() {
    let that = this;
    mui.back = function() {
      that.$router.push({
        name: "orderdetail",
        params: {
          type: 2
        }
      });
      error;
    };
    that
      .$http({
        url: "/Transaction/pay_info",
        method: "post",
        data: {
          token: that.$store.state.user_info.token,
          seller_id: that.$route.params.id,
          order_id: that.$route.params.id1
        }
      })
      .then(function(res) {
        if (res.data.code == 1) {
          that.info = res.data.data.list;
        } else {
          that.$vux.toast.show({
            text: res.data.msg,
            type: "cancel",
            position: "middle",
            time: 1200
          });
        }
      });
  },
  methods: {
    back() {
      this.$router.push({
        name: "orderdetail",
        params: {
          type: 2
        }
      });
    },
    showImg() {
      this.big_if = true;
    },
    sub() {
      let that = this;
      that.$vux.loading.show({
        text: ""
      });
      that
        .$http({
          url: "/Transaction/do_pay",
          method: "post",
          data: {
            token: that.$store.state.user_info.token,
            order_id: that.$route.params.id1
          }
        })
        .then(function(res) {
          that.$vux.loading.hide();
          if (res.data.code == 1) {
            that.$vux.alert.show({
              title: that.$t('topay.tip.t3'),
              content: that.$t('topay.tip.t2'),
              buttonText: that.$t('topay.tip.t4'),
              onShow() {},
              onHide() {
                that.dialog0 = false;
                that.$router.push({
                  name: "orderdetail",
                  params: {
                    type: 2
                  }
                });
              }
            });
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
  }
};
</script>
<style lang="less" scoped>
.wrap {
  font-size: 0.36rem;
  width: 100%;
  height: 100%;
  color: white;
  box-sizing: border-box;
  padding: 1.1rem 0 0rem;
  overflow: auto;
  .mask {
    position: fixed;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    background: rgba(0, 0, 0, 0.5);
    z-index: 999;
    div {
      width: 100%;
      height: 100%;
      img {
        max-width: 100%;
        max-height: 100%;
        display: block;
        margin: 0 auto;
      }
    }
  }
  .top {
    background: #1a212a;
  }
  .main {
    .d_btn {
      .btn {
        display: block;
        width: 92%;
        margin: 1rem auto 1rem;
        line-height: 0.8rem;
        text-align: center;
        color: #fcb90b;
        border-radius: 4px;
        background: transparent;
        border: 1px solid #fcb90b;
      }
    }
    .ul_title {
      padding: 0.4rem 4% 0.1rem;
      font-size: 0.4rem;
    }
    ul {
      background: #1a212a;
      padding: 0.2rem 4% 0.2rem;
      li {
        padding: 0.3rem 0 0.3rem;
        border-bottom: 1px solid #323b46;
        span {
          display: inline-block;
          color: #dee2ea;
          img {
            width: 2rem;
            height: 2rem;
            display: inline-block;
            vertical-align: top;
          }
        }
        span:first-child {
          width: 2.5rem;
          color: #8f8f9b;
        }
      }
      li:last-child {
        border: 0;
      }
    }
  }
}
</style>
