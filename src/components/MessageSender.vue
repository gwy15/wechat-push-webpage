<template>
  <div class="message-sender">
    <Form
      ref="senderForm"
      style="max-width: 500px; margin:auto; padding-right:10px;"
      :model="formData"
      :rules="ruleMessage"
      label-position="right"
      :label-width="60"
    >
      <FormItem label="ID" prop="openID">
        <Input v-model="formData.openID" placeholder="输入接收者的 Open ID" />
      </FormItem>
      <FormItem label="标题" prop="title">
        <Input v-model="formData.title" placeholder="输入消息标题" />
      </FormItem>
      <FormItem label="正文" prop="body">
        <Input
          v-model="formData.body"
          placeholder="输入消息正文（可选）."
          type="textarea"
          :autosize="{ minRows: 2, maxRows: 10 }"
        />
      </FormItem>
      <FormItem label="链接" prop="url">
        <Input
          v-model="formData.url"
          placeholder="输入消息后的跳转链接（可选）"
        />
      </FormItem>
      <FormItem>
        <Button type="primary" @click="handleSubmit('senderForm')">发送</Button>
        <Button v-if="success" @click="gotoDetail" style="margin-left: 8px">
          查看
        </Button>
      </FormItem>
    </Form>
  </div>
</template>

<script>
import { Form, FormItem, Input, Button } from "view-design";
import axios from "axios";
import { urls } from "@/utils/url";

export default {
  name: "MessageSender",
  components: {
    Form,
    FormItem,
    Input,
    Button
  },
  props: {
    openID: {
      type: String,
      default: ""
    },
    title: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      success: false,
      token: "",
      formData: {
        openID: this.openID,
        title: this.title,
        body: "",
        url: ""
      },
      ruleMessage: {
        openID: [{ required: true, message: "Open ID cannot be empty" }],
        title: [{ required: true, message: "Title cannot be empty" }]
      }
    };
  },
  methods: {
    handleSubmit(formName) {
      const app = this;
      const msg = app.$Message;
      this.$refs[formName].validate(valid => {
        if (!valid) {
          return;
        }

        const data = new URLSearchParams();
        data.append("receiver", app.formData.openID);
        data.append("title", app.formData.title);
        data.append("body", app.formData.body);
        data.append("url", app.formData.url);
        const removeLoading = msg.loading({
          content: "Message sending...",
          duration: 0
        });
        axios
          .post(urls.messageUrl(), data)
          .then(function(response) {
            removeLoading();
            msg.success("Request success");
            app.success = true;
            app.token = response.data.token;
          })
          .catch(function(err) {
            removeLoading();
            msg.error("Network request failed.");
            throw err;
          });
      });
    },
    gotoDetail() {
      this.$router.push({ name: "detail", params: { token: this.token } });
    }
  }
};
</script>

<style scoped>
div.message-sender {
  text-align: center;
}
</style>
