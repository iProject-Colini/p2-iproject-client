<template>
  <div class="page-content page-container" id="page-content">
    <div class="padding">
      <div class="row container d-flex justify-content-center">
        <!-- before joined -->

        <v-form ref="form" lazy-validation v-if="!joined">
          <v-text-field
            v-model="name"
            :counter="10"
            label="Name"
            @keyup.enter="join"
            required
          ></v-text-field>
          <v-btn color="success" class="mr-4" @click="join"> Chat! </v-btn>
        </v-form>
        <!-- after joined -->
        <div class="col-md-4">for admin</div>
        <div class="col-md-6" v-if="joined">
          <div class="card card-bordered">
            <div class="card-header">
              <h4 class="card-title">
                <strong>Chat</strong> -
                {{ $socket.connected ? "Connected" : "Disconnected" }}
              </h4>
              <a
                class="btn btn-xs btn-secondary"
                href="#"
                data-abc="true"
                @click.prevent="deleteName"
                >Quit</a
              >
            </div>
            <div
              class="ps-container ps-theme-default ps-active-y"
              id="chat-content"
              style="overflow-y: scroll !important; height: 400px !important"
            >
              <!-- message -->
              <chat-card v-for="(msg, i) in messages" :key="i" :message="msg" />

              <div class="ps-scrollbar-x-rail" style="left: 0px; bottom: 0px">
                <div
                  class="ps-scrollbar-x"
                  tabindex="0"
                  style="left: 0px; width: 0px"
                ></div>
              </div>
              <div
                class="ps-scrollbar-y-rail"
                style="top: 0px; height: 0px; right: 2px"
              >
                <div
                  class="ps-scrollbar-y"
                  tabindex="0"
                  style="top: 0px; height: 2px"
                ></div>
              </div>
            </div>
            <div class="publisher bt-1 border-light">
              <img
                class="avatar avatar-xs"
                src="https://img.icons8.com/color/36/000000/administrator-male.png"
                alt="..."
              />
              <input
                class="publisher-input"
                type="text"
                placeholder="Write something"
                v-model="inputMsg"
                @keyup.enter="sendMessage"
              />
              <button @click.prevent="sendMessage" class="btn btn-primary">
                Send
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ChatCard from "./ChatCard.vue";

export default {
  components: { ChatCard },
  name: "Chat",
  data() {
    return {
      joined: false,
      inputMsg: "",
      name: "",
      currentClient: localStorage.getItem("name"),
    };
  },
  methods: {
    async join() {
      await localStorage.setItem("name", this.name);
      this.$socket.client.emit("loginUser", this.name);
      this.joined = true;
    },
    sendMessage() {
      const data = {
        name: localStorage.getItem("name"),
        message: this.inputMsg,
      };
      // this.$store.commit("PUSH_MESSAGE", data);
      this.inputMsg = "";
      this.$socket.client.emit("newMessage", data);
    },
    async deleteName() {
      await this.$socket.client.emit("log", {
        name: this.name,
        log: this.$store.state.messages,
      });
      localStorage.removeItem("name");
      this.joined = false;
    },
  },
  computed: {
    messages() {
      return this.$store.state.messages;
    },
  },
  sockets: {
    connect() {
      console.log("socket connected");
    },
    sendMessage(data) {
      this.$store.commit("PUSH_MESSAGE", data.message);
    },
    sendDataAdmin(data) {
      this.$store.commit("ADMIN_DATA", data);
    },
    // logMessage(data) {
    //   console.log(data);

    //   this.$store.commit("GET_MESSAGE", data);
    // },
  },
  created() {
    if (localStorage.getItem("name")) {
      this.joined = true;
      this.name = localStorage.getItem("name");
    }
  },
};
</script>

<style>
.card-bordered {
  border: 1px solid #ebebeb;
}

.card {
  border: 0;
  border-radius: 0px;
  margin-bottom: 30px;
  -webkit-box-shadow: 0 2px 3px rgba(0, 0, 0, 0.03);
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.03);
  -webkit-transition: 0.5s;
  transition: 0.5s;
}

.padding {
  padding: 3rem !important;
}

body {
  background-color: #f9f9fa;
}

.card-header:first-child {
  border-radius: calc(0.25rem - 1px) calc(0.25rem - 1px) 0 0;
}

.card-header {
  display: -webkit-box;
  display: flex;
  -webkit-box-pack: justify;
  justify-content: space-between;
  -webkit-box-align: center;
  align-items: center;
  padding: 15px 20px;
  background-color: transparent;
  border-bottom: 1px solid rgba(77, 82, 89, 0.07);
}

.card-header .card-title {
  padding: 0;
  border: none;
}

h4.card-title {
  font-size: 17px;
}

.card-header > *:last-child {
  margin-right: 0;
}

.card-header > * {
  margin-left: 8px;
  margin-right: 8px;
}

.btn-secondary {
  color: #4d5259 !important;
  background-color: #e4e7ea;
  border-color: #e4e7ea;
  color: #fff;
}

.btn-xs {
  font-size: 11px;
  padding: 2px 8px;
  line-height: 18px;
}

.btn-xs:hover {
  color: #fff !important;
}

.card-title {
  font-family: Roboto, sans-serif;
  font-weight: 300;
  line-height: 1.5;
  margin-bottom: 0;
  padding: 15px 20px;
  border-bottom: 1px solid rgba(77, 82, 89, 0.07);
}

.ps-container {
  position: relative;
}

.ps-container {
  -ms-touch-action: auto;
  touch-action: auto;
  overflow: hidden !important;
  -ms-overflow-style: none;
}

.media-chat {
  padding-right: 64px;
  margin-bottom: 0;
}

.media {
  padding: 16px 12px;
  -webkit-transition: background-color 0.2s linear;
  transition: background-color 0.2s linear;
}

.media .avatar {
  flex-shrink: 0;
}

.avatar {
  position: relative;
  display: inline-block;
  width: 36px;
  height: 36px;
  line-height: 36px;
  text-align: center;
  border-radius: 100%;
  background-color: #f5f6f7;
  color: #8b95a5;
  text-transform: uppercase;
}

.media-chat .media-body {
  -webkit-box-flex: initial;
  flex: initial;
  display: table;
}

.media-body {
  min-width: 0;
}

.media-chat .media-body p {
  position: relative;
  padding: 6px 8px;
  margin: 4px 0;
  background-color: #f5f6f7;
  border-radius: 3px;
  font-weight: 100;
  color: #9b9b9b;
}

.media > * {
  margin: 0 8px;
}

.media-chat .media-body p.meta {
  background-color: transparent !important;
  padding: 0;
  opacity: 0.8;
}

.media-meta-day {
  -webkit-box-pack: justify;
  justify-content: space-between;
  -webkit-box-align: center;
  align-items: center;
  margin-bottom: 0;
  color: #8b95a5;
  opacity: 0.8;
  font-weight: 400;
}

.media {
  padding: 16px 12px;
  -webkit-transition: background-color 0.2s linear;
  transition: background-color 0.2s linear;
}

.media-meta-day::before {
  margin-right: 16px;
}

.media-meta-day::before,
.media-meta-day::after {
  content: "";
  -webkit-box-flex: 1;
  flex: 1 1;
  border-top: 1px solid #ebebeb;
}

.media-meta-day::after {
  content: "";
  -webkit-box-flex: 1;
  flex: 1 1;
  border-top: 1px solid #ebebeb;
}

.media-meta-day::after {
  margin-left: 16px;
}

.media-chat.media-chat-reverse {
  padding-right: 12px;
  padding-left: 64px;
  -webkit-box-orient: horizontal;
  -webkit-box-direction: reverse;
  flex-direction: row-reverse;
}

.media-chat {
  padding-right: 64px;
  margin-bottom: 0;
}

.media {
  padding: 16px 12px;
  -webkit-transition: background-color 0.2s linear;
  transition: background-color 0.2s linear;
}

.media-chat.media-chat-reverse .media-body p {
  float: right;
  clear: right;
  background-color: #48b0f7;
  color: #fff;
}

.media-chat .media-body p {
  position: relative;
  padding: 6px 8px;
  margin: 4px 0;
  background-color: #f5f6f7;
  border-radius: 3px;
}

.border-light {
  border-color: #f1f2f3 !important;
}

.bt-1 {
  border-top: 1px solid #ebebeb !important;
}

.publisher {
  position: relative;
  display: -webkit-box;
  display: flex;
  -webkit-box-align: center;
  align-items: center;
  padding: 12px 20px;
  background-color: #f9fafb;
}

.publisher > *:first-child {
  margin-left: 0;
}

.publisher > * {
  margin: 0 8px;
}

.publisher-input {
  -webkit-box-flex: 1;
  flex-grow: 1;
  border: none;
  outline: none !important;
  background-color: transparent;
}

button,
input,
optgroup,
select,
textarea {
  font-family: Roboto, sans-serif;
  font-weight: 300;
}

.publisher-btn {
  background-color: transparent;
  border: none;
  color: #8b95a5;
  font-size: 16px;
  cursor: pointer;
  overflow: -moz-hidden-unscrollable;
  -webkit-transition: 0.2s linear;
  transition: 0.2s linear;
}

.file-group {
  position: relative;
  overflow: hidden;
}

.publisher-btn {
  background-color: transparent;
  border: none;
  color: #cac7c7;
  font-size: 16px;
  cursor: pointer;
  overflow: -moz-hidden-unscrollable;
  -webkit-transition: 0.2s linear;
  transition: 0.2s linear;
}

.file-group input[type="file"] {
  position: absolute;
  opacity: 0;
  z-index: -1;
  width: 20px;
}

.text-info {
  color: #48b0f7 !important;
}
</style>
