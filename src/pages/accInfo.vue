<template>
  <div class="wrap">
    <div class="top">
      <img @click="back" class="back_img" src="../assets/nav_back.png" alt>
      <div>{{$t('accinfo.title')}}</div>
    </div>
    <div class="main">
      <div class="list_div">
        <ul>
          <li @click="show=false">
            <span class="f_l">{{$t('accinfo.list.l1')}}</span>
            <span class="f_r iconfont icon-youjiantou"></span>
            <img class="f_r" :src="src" alt>
            <input
              name="img"
              type="file"
              id="handcard"
              @change="pushImg1($event)"
              accept="image/jpeg, image/png, image/gif"
              alt
            >
          </li>
          <li @click="uName">
            <span class="f_l">{{$t('accinfo.list.l2')}}</span>
            <span class="f_r iconfont icon-youjiantou"></span>
            <span class="f_r">{{name}}</span>
          </li>
          <li>
            <span class="f_l">ID</span>
            <span class="f_r iconfont icon-youjiantou"></span>
            <span class="f_r">{{info.id}}</span>
          </li>
        </ul>
      </div>
    </div>
    <actionsheet class="sheet" v-model="show" :menus="menus" @on-click-menu="click" show-cancel></actionsheet>
  </div>
</template>
<script>
import $ from "jquery";
import { Actionsheet } from "vux";
export default {
  data() {
    return {
      show: false,
      src: "",
      name: "",
      info: {},
      menus: {
        menu1: "拍照",
        menu2: "从相册选择"
      }
    };
  },
  components: {
    Actionsheet
  },
  mounted() {
    let that = this;
    mui.back = function() {
      that.$router.back();
      error;
    };
  },
  activated() {
    let that = this;
    that
      .$http({
        url: "/user/user_head_info",
        method: "post",
        data: {
          token: that.$store.state.user_info.token
        }
      })
      .then(function(res) {
        if (res.data.code == 1) {
          that.info = res.data.data.user_info;
          that.src = that.info.head_icon;
          that.name = that.info.name;
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
    click(key) {
      if (key == "menu1") {
        getImage();
      } else {
        galleryImgs();
      }
    },
    pushImg1: function(e) {
      let file = e.target,
        reader = new FileReader(),
        that = this,
        _name,
        _fileName;
      _name = file.value;
      _fileName = _name.substring(_name.lastIndexOf(".") + 1).toLowerCase();
      // if (_fileName !== "png" && _fileName !== "jpg") {
      //   that.$vux.toast.show({
      //     text: "请上传图片类型文件！",
      //     type: "cancel",
      //     position: "middle",
      //     time: 1200
      //   });
      // }else{
      reader.readAsDataURL(file.files[0]);
      if (file.files[0].size > 10 * 1024 * 1024) {
        that.$vux.toast.show({
          text: that.$t("ups.d"),
          type: "warn",
          position: "middle",
          time: 1500
        });
      } else {
        that.$vux.loading.show({
          text: that.$t('load.uping')
        });
        reader.onload = function() {
          let result = this.result;

          // that.data1 = result;

          var image = new FormData();
          image.append("file", e.target.files[0]);
          image.append("type", "4");
          image.append("token", that.$store.state.user_info.token);
          that
            .$http({
              url: "/user/upload_pic",
              method: "post",
              data: image
            })
            .then(function(res) {
              that.$vux.loading.hide();
              if (res.data.code == 1) {
                that.data1 = result;
                that.src = res.data.url;
                that
                  .$http({
                    url: "/user/change_user_info",
                    method: "post",
                    data: {
                      token:that.$store.state.user_info.token,
                      type:1,
                      profile_icon:that.src,
                      nickname:""
                    }
                  })
                  .then(function(res) {
                    if (res.data.code == 1) {
                      
                    } else {
                      that.$vux.toast.show({
                        text: res.data.msg,
                        type: "cancel",
                        position: "middle",
                        time: 1200
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
        };
      }

      // }
    },
    //调用手机摄像头并拍照
    getImage() {
      var cmr = plus.camera.getCamera();
      cmr.captureImage(
        function(p) {
          plus.io.resolveLocalFileSystemURL(
            p,
            function(entry) {
              compressImage(entry.toLocalURL(), entry.name);
            },
            function(e) {
              plus.nativeUI.toast("读取拍照文件错误：" + e.message);
            }
          );
        },
        function(e) {},
        {
          filter: "image"
        }
      );
    },
    //从相册选择照片
    galleryImgs() {
      plus.gallery.pick(
        function(e) {
          var name = e.substr(e.lastIndexOf("/") + 1);
          compressImage(e, name);
        },
        function(e) {},
        {
          filter: "image"
        }
      );
    },
    uName() {
      this.$router.push({
        name: "changename"
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
  overflow: auto;
  .top {
    background: #1a212a;
  }
  .sheet {
    color: #000;
  }
  .main {
    .list_div {
      ul {
        margin-bottom: 0.1rem;
        li {
          position: relative;
          background: #1a212a;
          margin-bottom: 1px;
          overflow: hidden;
          padding: 0.2rem 4% 0.2rem;
          line-height: 1.1rem;
          input {
            z-index: 9;
            opacity: 0;
            position: absolute;
            width: 100%;
            height: 100%;
            left: 0;
            top: 0;
          }
          .f_r {
            color: #9b9993;
          }
          .iconfont {
            color: #888;
            font-size: 0.6rem;
            margin-left: 0.4rem;
          }
          img {
            width: 0.8rem;
            height: 0.8rem;
            transform: translateY(3px);
          }
        }
        li:last-child {
          margin-bottom: 0;
          .iconfont {
            visibility: hidden;
          }
        }
      }
    }
  }
}
</style>
