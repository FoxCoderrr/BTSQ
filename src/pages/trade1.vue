<template>
  <div class="wrap" :class="{wrapp:!$store.state.bshow}">
    <div class="top">
      <keep-alive>
        <tab
          class="tab"
          :line-width="2"
          bar-active-color="#f0b90b"
          active-color="#f0b90b"
          :scroll-threshold="5"
          v-model.number="type"
        >
          <tab-item @click.native="navTap(0)">{{$t('trade.title.t1')}}</tab-item>
          <tab-item @click.native="navTap(1)">{{$t('trade.title.t2')}}</tab-item>
          <tab-item @click.native="navTap(2)">{{$t('trade.title.t3')}}</tab-item>
        </tab>
      </keep-alive>
    </div>
    <router-view name="child" class="child"></router-view>
  </div>
</template>
<script>
import $ from "jquery";
import {Tab, TabItem } from "vux";

export default {
  data() {
    return {
      type:0,
    };
  },
  components: {
    Tab,
    TabItem,
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
      if (this.$store.state.n) {
        this.type = Number(this.$store.state.n);
      } else {
        this.type = 0;
      }
    });
  },
  methods: {
    navTap(i) {
      this.$store.state.n = i;
      if (i == 0) {
        this.$router.push({
          name: "trade1e",
          params:{
            type:0
          }
        });
      } else if (i == 1) {
        this.$router.push({
          name: "orderdetail",
          params:{
            type:0
          }
        });
      } else {
        this.$router.push({
          name: "dealhistory"
        });
      }
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
  overflow: auto;
  .top {
    background: #1a212a;
  }
}
.wrapp{
  padding: 1.3rem 0 0.1rem;
}
</style>
