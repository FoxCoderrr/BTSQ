<template>
  <div class="wrap">
    <div class="top">
      <img @click="back" class="back_img" src="../assets/nav_back.png" alt>
      <div>{{$t('wallet.btn.b2')}}</div>
    </div>
    <div class="div">{{ads}}</div>
    <div>
      <button
        class="btn"
        v-clipboard:copy="ads"
        v-clipboard:success="onCopy"
        v-clipboard:error="onError"
      >{{$t('wallet.btn.b3')}}</button>
    </div>
  </div>
</template>
<script>
import $ from "jquery";

export default {
  data() {
    return {
      ads: ""
    };
  },
  components: {},
  mounted() {
    let that = this;
    mui.back = function() {
      that.$router.back();
      error;
    };
    that
      .$http({
        url: "/wallet/receivablesPage",
        method: "post",
        data: {
          token: that.$store.state.user_info.token,
          id: that.$store.state.active_wallet.id
        }
      })
      .then(function(res) {
        if (res.data.code == 1) {
          that.ads = res.data.data;
        } else {
          that.$vux.toast.show({
            text: res.data.msg,
            type: "text",
            position: "middle",
            time: 1200
          });
        }
      });
  },
  methods: {
    back() {
      this.$router.back();
    },
    onCopy(e) {
      this.$vux.toast.show({
        text: this.$t('tip.copy_suc'),
        type: "success",
        position: "middle",
        time: 1200
      });
    },
    // 复制失败
    onError(e) {
      this.$vux.toast.show({
        text: this.$t('tip.copy_error'),
        type: "cancel",
        position: "middle",
        time: 1200
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
  padding: 1.3rem 0 1.5rem;
  overflow: hidden;
  .top {
    background: #1a212a;
  }
  .div {
    margin: 0 6% 2rem;
    padding: 3rem 0 0.5rem;
    box-sizing: border-box;
    border-bottom: 1px solid #323b46;
    text-align: center;
    word-break: break-all;
  }
  .btn {
    display: block;
    width: 88%;
    margin: 0 auto;
    line-height: 0.8rem;
    text-align: center;
    color: #fcb90b;
    border-radius: 4px;
    background: transparent;
    border: 1px solid #fcb90b;
  }
}
</style>
