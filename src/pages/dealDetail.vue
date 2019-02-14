<template>
  <div class="wrap">
    <div class="top">
      <img @click="back" class="back_img" src="../assets/nav_back.png" alt>
      <div>{{$t('trade.child3.detail')}}</div>
    </div>
    <div class="main">
        <ul>
            <li>
                <span>{{$t('trade.child3.list.t1')}}：</span>
                <span>{{info.order}}</span>
            </li>
            <li>
                <span>{{$t('trade.child3.list.t2')}}：</span>
                <span :class="[info.trade_type_text_color=='red'?'f_c_red':'f_c_green']">{{info.trade_type_text}}</span>
            </li>
            <li>
                <span>{{$t('trade.child2.list.t2')}}：</span>
                <span>{{info.price}}</span>
            </li>
            <li>
                <span>{{$t('trade.child2.list.t3')}}：</span>
                <span>{{info.number}}</span>
            </li>
            <li>
                <span>{{$t('trade.child2.list.t6')}}：</span>
                <span>{{info.total}}</span>
            </li>
            <li>
                <span>{{$t('trade.child2.list.t1')}}：</span>
                <span>{{info.done_date}}</span>
            </li>
            <li>
                <span>{{$t('trade.child3.list.t3')}}：</span>
                <span>{{info.order_status_text}}</span>
            </li>
            
        </ul>
    </div>
  </div>
</template>
<script>
import $ from "jquery";
export default {
  data() {
    return {
        info:{},
    };
  },
  components: {
    
  },
  mounted() {
    let that = this;
    mui.back = function() {
      that.$router.back();
      error;
    };
    that
      .$http({
        url: "/Transaction/view_details",
        method: "post",
        data: {
          token: that.$store.state.user_info.token,
          trade_id: that.$route.params.id
        }
      })
      .then(function(res) {
        if (res.data.code == 1) {
          that.info = res.data.data;
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
  .main{
      ul{
          background: #1a212a;
          padding: 0.2rem 10% 0.2rem;
          li{
              padding: 0.2rem 0 0.2rem;
              span{
                  display: inline-block;
              }
              span:first-child{
                  width: 2rem;
              }
              span:last-child{
                  color: #8f8f9b;
              }
          }
      }
  }

}
</style>
