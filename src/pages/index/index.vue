<template>
  <div class="container" @click="clickHandle('test click', $event)">

    <div class="userinfo" @click="bindViewTap">
      <img class="userinfo-avatar" v-if="userInfo.avatarUrl" :src="userInfo.avatarUrl" background-size="cover" />
      <div class="userinfo-nickname">
        <card :text="userInfo.nickName"></card>
      </div>
    </div>

    <div class="usermotto">
      <div class="user-motto">
        <card :text="motto"></card>
      </div>
    </div>

    <form class="form-container">
      <input type="text" class="form-control" v-model="motto" placeholder="v-model" />
      <input type="text" class="form-control" v-model.lazy="motto" placeholder="v-model.lazy" />
    </form>
  </div>
</template>

<script>
import card from "@/components/card";
import { createClass } from "asteroid";
// const WebSocket = require('ws');
var DDPClient = require("ddp-client");

export default {
  data() {
    return {
      motto: "Hello World",
      userInfo: {},
      inventory_list: []
    };
  },

  components: {
    card
  },

  methods: {
    bindViewTap() {
      const url = "../logs/main";
      wx.navigateTo({ url });
    },
    getUserInfo() {
      // 调用登录接口
      wx.login({
        success: () => {
          wx.getUserInfo({
            success: res => {
              this.userInfo = res.userInfo;
            }
          });
        }
      });
    },
    clickHandle(msg, ev) {
      console.log("clickHandle:", msg, ev);
    }
  },

  created() {
    // Connect to a Meteor backend
    // const Asteroid = createClass();
    // const asteroid = new Asteroid({
    //   endpoint: "ws://localhost:3000/websocket",
    //   SocketConstructor: function(endpoint) {
    //     return wx.connectSocket({
    //       url: "ws://localhost:3000/websocket"
    //     });
    //   }
    // });
    var ddpclient = new DDPClient({
      // All properties optional, defaults shown
      host: "localhost",
      port: 3000,
      ssl: false,
      autoReconnect: true,
      autoReconnectTimer: 500,
      maintainCollections: true,
      ddpVersion: "1", // ['1', 'pre2', 'pre1'] available
      // Use a full url instead of a set of `host`, `port` and `ssl`
      url: "wss://localhost:3000/websocket",
      SocketConstructor: function(endpoint) {
        return wx.connectSocket({
          url: "ws://localhost:3000/websocket"
        });
      }
    });
    var observer = ddpclient.observe("platforms.all");
    observer.added = function(id) {
      console.log("[ADDED] to " + observer.name + ":  " + id);
    };

    // const platform_id = "vMtyaQA2phqDNQZ9D";
    // asteroid.subscribe("platforms.all");
    // console.log("sub id: " + asteroid.subscribe("platforms.all").id);

    // console.log("meteor ddp");
    // asteroid.ddp.on("added", function({ collection, id, fields }) {
    //   console.log("added");
    //   console.log(fields);
    //   let contians = this.inventory_list.includes(item => {
    //     item.id === id;
    //   });
    //   if (!contians) {
    //     fields.id = id;
    //     this.inventory_list.push(fields);
    //   }
    // });

    // 调用应用实例的方法获取全局数据
    this.getUserInfo();
  }
};
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 128rpx;
  height: 128rpx;
  margin: 20rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

.usermotto {
  margin-top: 150px;
}

.form-control {
  display: block;
  padding: 0 12px;
  margin-bottom: 5px;
  border: 1px solid #ccc;
}
</style>
