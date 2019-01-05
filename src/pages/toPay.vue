<template>
  <div class="wrap">
    <div class="top">
      <img @click="back" class="back_img" src="../assets/nav_back.png" alt>
      <div>支付信息</div>
    </div>
    <div class="main">
      <div class="ul_title">银行卡信息</div>
      <ul>
        <li>
          <span>银行卡账号：</span>
          <span>13300000001</span>
        </li>
        <li>
          <span>开户行：</span>
          <span>中国建设银行</span>
        </li>
        <li>
          <span>开户姓名：</span>
          <span>张三</span>
        </li>
        <li>
          <span>开户行资料：</span>
          <span>北京北京市朝阳区支行</span>
        </li>
      </ul>
      <div class="ul_title">支付宝信息</div>
      <ul>
        <li>
          <span>支付宝账号：</span>
          <span>13300000001</span>
        </li>
        <li>
          <span>真实姓名：</span>
          <span>张三</span>
        </li>
      </ul>
      <div class="ul_title">微信信息</div>
      <ul>
        <li>
          <span>微信昵称：</span>
          <span>13300000001</span>
        </li>
        <li>
          <span>收款码：</span>
          <span>
            <img src="../assets/logo.png" alt @click="showImg">
          </span>
        </li>
      </ul>
      <div class="d_btn">
        <button class="btn" @click="sub">我已完成支付，点击确认</button>
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
      src: src
    };
  },
  components: {},
  mounted() {
    let that = this;
    mui.back = function() {
      that.$router.back();
      error;
    };
  },
  methods: {
    back() {
      this.$router.back();
    },
    showImg() {
      this.big_if = true;
    },
    sub() {
      let that = this;
      that.$vux.alert.show({
        title: "成功",
        content: "确认支付成功！",
        buttonText: "确认",
        onShow() {},
        onHide() {
          that.dialog0 = false;
          that.$router.push({
            name: "orderdetail",
            params:{
                type:2
            }
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
