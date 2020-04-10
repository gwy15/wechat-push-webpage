<template>
  <div class="detail-page">
    <h2 class="title">{{ title }}</h2>
    <p class="created-time">{{ createdTime.fromNow() }}</p>
    <div class="message-body" v-html="compiledMarkdown"></div>

    <div v-if="url" class="url">
      <a :href="url">查看链接</a>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import marked from "marked";
import moment from "moment";
import { urls } from "@/utils/url";

export default {
  name: "DetailPage",
  data: function() {
    return {
      title: "加载中",
      body: "正在加载数据",
      createdTime: moment(),
      url: null
    };
  },
  created: function() {
    const locale =
      window.navigator.userLanguage || window.navigator.language || "zh-CN";
    moment.locale(locale);
  },
  mounted: function() {
    const app = this;
    const token = app.$route.params.token;
    if (token == null) {
      app.title = "";
      app.body = "";
      return;
    }
    // api request
    const apiUrl = urls.messageUrl(token);
    axios
      .get(apiUrl)
      .then(function(response) {
        const data = response.data;
        app.title = data.title;
        app.body = data.body;
        app.createdTime = moment.unix(data.created_time);
        app.url = data.url;
      })
      .catch(function(err) {
        if (err.response.status == 404) {
          app.title = "消息不存在";
          app.body = "未找到对应消息，请检查您的链接。";
        } else {
          app.title = "发生错误";
          app.body = "发生了一些错误";
        }
      });
  },
  computed: {
    compiledMarkdown: function() {
      return marked(this.body);
    }
  }
};
</script>

<style lang="less">
.detail-page {
  max-width: 100vw;
  overflow-y: auto;
  word-wrap: break-word;
  word-break: break-all;
  .title {
    font-size: 30px;
    margin: 15px 0 20px;
  }
  .created-time {
    text-align: right;
    margin: 5px;
  }
  .url {
    text-align: left;
  }
  .message-body {
    margin-top: 10px;
    text-align: left;

    p {
      line-height: 1.6;
    }
  }
}
</style>
