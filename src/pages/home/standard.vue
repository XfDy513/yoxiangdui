<template>
  <div class="standard">
    <div class="nav">
      <div class="navList" @click="getText()">
        <img src="../../assets/img/picText.png" alt />
        <p>本机查看图文详情</p>
      </div>
      <div class="navList" @click="openApp()">
        <div class="twoCode" id="twoCodeqrcode" ref="twoCodeqrcode"></div>
        <p>手机查看图文详情</p>
      </div>
    </div>
    <div class="footer" @click="openChange">
      <span>已知晓, 下一步</span>
    </div>
  </div>
</template>
<script>
import * as apiHttp from "../../api/index";
import { qrcanvas } from "qrcanvas";
import store from "../../store";
export default {
  data() {
    return {
      list: [],
      goodsId: this.$route.query.goodsId,
      index: this.$route.query.index,
      productId: this.$route.query.productId,
      showCode: false,
      showOne: false
    };
  },

  mounted() {
    let that = this;
    apiHttp
      .getgoodsProduct(this.goodsId)
      .then(res => {
        if (res.code === 1) {
          that.list = res.data;
          that.qrcode();
        }
      });
      // 获取设备
    apiHttp.getgoodsDetail(this.productId).then(res => {
        if (res.code === 1) {
        }
      });
  },
  methods: {
    getText() {
      var browser = api.require("webBrowser");
      browser.open({
        url: this.list[0].content_url
      });
    },
    qrcode: function() {
      let canvas1 = qrcanvas({
        data: this.list[this.index].content_url,
        size: 100
      });
      document.getElementById("twoCodeqrcode").appendChild(canvas1);
    },
    openChange(num) {
      let type = this.list[this.index].dui.type;
      let link = this.list[this.index].link;
      let androidPkg = this.list[this.index].package;
      let id = this.list[this.index].dui.id;

      let that = this;
      if (type.indexOf("4") >= 0) {
        that.$router.push({
          name: "integral",
          query: {
            id: id
          }
        });
      } else if (link) {
        var bdTTS = api.require("bdTTS");
        bdTTS.speakPause(function(ret) {});
        that.showCode = false;
        let text =
          "即将打开银行链接，按照兑换流程进行兑换操作，切记兑换过程中需要点击小浮窗上面的地址获取地址信息，兑换完成请截屏，截屏成功再点击小浮窗上面的报单按钮，回到兑换页面，点击兑换好了去报单。";
        bdTTS.speak(
          {
            text: text
          },
          function(ret) {}
        );
        that.$router.go(-1)
        api.openApp(
          {
            androidPkg: "android.intent.action.VIEW",
            mimeType: "text/html",
            uri: link
          },
          function(ret, err) {
            // if(ret){
            //   that.$router.go(-1)
            // }
          }
        );
      } else if (androidPkg) {
        var bdTTS = api.require("bdTTS");
        bdTTS.speakPause(function(ret) {});
        that.showCode = false;
        let text =
          "即将打开银行链接，按照兑换流程进行兑换操作，切记兑换过程中需要点击小浮窗上面的地址获取地址信息，兑换完成请截屏，截屏成功再点击小浮窗上面的报单按钮，回到兑换页面，点击兑换好了去报单。";
        bdTTS.speak(
          {
            text: text
          },
          function(ret) {}
        );
        let appManagerPlus = api.require("appManagerPlus");
        let Getsn = api.require("moduleDemo");
        
        appManagerPlus.isInstalled(
          {
            paramType: 0,
            paramStr: androidPkg
          },
          function(ret) {
            if (ret.status) {
              that.$router.go(-1)
              api.openApp({ androidPkg: androidPkg },function(){
                // if(ret){
                //   that.$router.go(-1)
                // }
              });
            } else {
              Getsn.startActivity({
                packageName: androidPkg
              });
            }
          }
        );
      } else {
        this.$router.push({
          name: "baodan",
          query: {
            id: this.list.id
          }
        });
      }
    }
  }
};
</script>

<style lang="less">
#showCode .weui-dialog {
  background-color: transparent;
  z-index: 5010;
}
#showCode {
  .name {
    padding: 0.3rem 1.3rem;
    font-size: 0.5rem;
    color: #fc3357;
    background: #fff;
  }
  .twoCode {
    padding-bottom: 0.2rem;
    background: #fff;
    border-bottom-left-radius: 3px;
    border-bottom-right-radius: 3px;
  }
  .close {
    margin-top: 1rem;
    width: 0.5rem;
  }
}
</style>

<style lang="less" scoped>
.standard {
  height: 100%;
  background: #f2f2f2;
  padding-top: 0.5rem;
  box-sizing: border-box;
  .nav {
    margin: 0 0.3rem;
    border-radius: 0.2rem;
    padding: 0.5rem 0;
    display: flex;
    justify-content: space-around;
    background: #ffffff;
    .navList {
      text-align: center;
      font-size: 0.28rem;
      img {
        width: 100px;
      }
    }
  }
  .footer {
    position: absolute;
    left: 0.3rem;
    right: 0.3rem;
    bottom: 0.2rem;
    height: 0.88rem;
    background: rgb(230, 80, 19);
    color: #fff;
    text-align: center;
    line-height: 0.88rem;
    font-size: 0.34rem;
  }
}
</style>