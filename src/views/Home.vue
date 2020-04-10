<template>
  <div class="home-page">
    <!-- 等待扫描结果确认 -->
    <div v-if="is_scanning">
      <h2>微信扫描二维码以获取 Open ID</h2>
      <div class="qr-code-box">
        <img :src="scan.qr_url" />
        <Spin fix size="large" v-if="scan.loading_qrcode">
          <div class="loader">
            <Icon type="ios-loading" size="18" class="spin-icon-load"></Icon>
          </div>
        </Spin>
      </div>
    </div>
    <!-- 扫描成功 -->
    <div class="scan-success" v-else>
      <p>你的 Open ID 为</p>
      <div class="open-id">
        {{ result.open_id }}
      </div>
      <Divider> 尝试发送一条信息 </Divider>
      <MessageSender :openID="result.open_id" title="Hello, world!" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import { Spin, Icon, Divider } from "view-design";
import { urls } from "@/utils/url";
import MessageSender from "@/components/MessageSender";

export default {
  name: "home",
  components: {
    MessageSender,
    Spin,
    Icon,
    Divider
  },
  data: function() {
    // return {
    //   is_scanning: true,
    //   scan: {
    //     loading_qrcode: true,
    //     scene_id: null,
    //     qr_url: "https://via.placeholder.com/400x400?text=Loading"
    //   },
    //   result: {
    //     open_id: null
    //   }
    // };
    return {
      is_scanning: false,
      scan: {
        loading_qrcode: true,
        scene_id: null,
        qr_url: "https://via.placeholder.com/400x400?text=Loading"
      },
      result: {
        open_id: "OPEN_ID"
      }
    };
  },
  mounted: function() {
    const app = this;
    axios
      .post(urls.sceneUrl())
      .then(function(response) {
        let data = response.data;
        app.scan.qr_url = data.qr_url;
        app.scan.scene_id = data.scene_id;
        window.setTimeout(app.checkScene, 1000);
        // 一秒后再解除加载
        window.setTimeout(() => {
          app.scan.loading_qrcode = false;
        }, 1000);
      })
      .catch(function(e) {
        app.$Message.error("加载二维码失败");
        throw e;
      });
  },
  computed: {
    expireTime: function() {
      moment.locale("zh-CN");
      const t = moment.unix(this.scene.expire_at).fromNow();
      return t + " 过期";
    }
  },
  methods: {
    /**
     * 检查二维码对应的 scene 是否已经被扫描
     */
    checkScene: function() {
      const app = this;
      if (!app.is_scanning) {
        return;
      }
      axios
        .get(urls.sceneUrl(app.scan.scene_id))
        .then(function(response) {
          let data = response.data;
          app.result.open_id = data.openID;
          app.is_scanning = false;
        })
        .catch(function(err) {
          if (err.response.status == 404) {
            // not yet.
            window.setTimeout(app.checkScene, 1000);
          } else {
            app.$Message.error("检查扫描结果失败");
            throw err;
          }
        });
    }
  }
};
</script>

<style lang="less">
.scan-success {
  .ivu-divider-inner-text {
    font-size: 14px;
    color: #a6a8aa;
  }
}
</style>

<style scoped lang="less">
.qr-code-box {
  position: relative;

  img {
    margin: 50px;
    width: 300px;
    height: 300px;
    overflow: hidden;
  }
  .spin-icon-load {
    animation: ani-demo-spin 1s linear infinite;
  }
  @keyframes ani-demo-spin {
    from {
      transform: rotate(0deg);
    }
    50% {
      transform: rotate(180deg);
    }
    to {
      transform: rotate(360deg);
    }
  }
}
.scan-success {
  .open-id {
    margin: 30px 0;
    font-weight: bold;
    font-size: 24px;
  }
}
</style>
