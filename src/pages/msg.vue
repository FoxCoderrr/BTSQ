<template>
  <div class="wrap">
    <div class="top">
      <img @click="back" class="back_img" src="../assets/nav_back.png" alt>
      <tab
        class="tab top_tab"
        :line-width="2"
        bar-active-color="#f0b90b"
        active-color="#f0b90b"
        :scroll-threshold="5"
      >
        <tab-item selected @click.native="show=true">{{$t('msg.title.t1')}}</tab-item>
        <tab-item @click.native="show=false">{{$t('msg.title.t2')}}</tab-item>
      </tab>
    </div>
    <ul v-if="show">
      <li v-for="(item,index) in list" :key="index" @click="toDetail(item.id,0)">
        <span class="f_l">
          <span>
            {{item.title}}
            <div></div>
          </span>
        </span>
        <img class="f_r r_img" src="../assets/right1.png" alt>
      </li>
    </ul>
    <ul v-if="!show">
      <li v-for="(item,index) in list" :key="index" @click="toDetail(item.id,1)">
        <span class="f_l">{{item.title}}</span>
        <img class="f_r r_img" src="../assets/right1.png" alt>
      </li>
    </ul>
  </div>
</template>
<script>
import $ from "jquery";
import { Tab, TabItem } from "vux";

export default {
  data() {
    return {
      show: true,
      list: [],
      type: 0
    };
  },
  components: {
    Tab,
    TabItem
  },
  mounted() {
    let that = this;
    mui.back = function() {
      that.$router.push({
        name: "mine"
      });
      error;
    };
  },
  activated() {
    let that = this;
    // that.$nextTick(function() {
    //   that.type = 0;
    // });
    that
      .$http({
        url: "/news/NewsListPage",
        method: "post",
        data: {
          token: that.$store.state.user_info.token
        }
      })
      .then(function(res) {
        if (res.data.code == 1) {
          that.list = res.data.data.list;
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
      this.$router.push({
        name: "mine"
      });
    },
    navTap(i) {
      if (i == 0) {
        this.$router.push({
          name: "message"
        });
      } else if (i == 1) {
        this.$router.push({
          name: "mymessage"
        });
      }
    },
    toDetail(id,type){
      let that = this;
      if(type==0){

      }else{

      }
      that.$router.push({
        name:"msgdetail",
        params:{
          i:id
        }
      })
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
  padding: 1.3rem 0 0;
  overflow: auto;
  .top {
    background: #1a212a;
  }
  ul {
    background: #1a212a;
    li {
      overflow: hidden;
      padding: 0.3rem 4% 0.3rem;
      line-height: 0.7rem;
      border-bottom: 1px solid #242424;
      .f_l {
        width: calc(100% - 1.5rem);
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        padding-right: 10px;
        box-sizing: border-box;
        > span {
          max-width: 100%;
          display: inline-block;
          position: relative;
          > div {
            position: absolute;
            width: 6px;
            height: 6px;
            background: #e53917;
            border-radius: 50%;
            right: -10px;
            top: 2px;
          }
        }
      }
      .r_img {
        width: 0.7rem;
      }
    }
  }
}
</style>
