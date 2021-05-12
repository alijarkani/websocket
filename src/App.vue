<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col>
            <v-card>
              <v-card-text>
                <v-row>
                  <v-col>
                    <v-text-field v-model="url" placeholder="echo.websocket.org" filled hide-details @keyup.enter="connect">
                      <template v-slot:prepend-inner>
                        <v-menu offset-y>
                          <template v-slot:activator="{ on, attrs }">
                            <v-btn
                              color="grey darken-4"
                              dark
                              v-bind="attrs"
                              v-on="on"
                              text
                              large
                              class="mt-n3 px-0"
                            >
                              <v-icon small>mdi-menu-down</v-icon>
                              {{protocol}}://
                            </v-btn>
                          </template>
                          <v-list>
                            <v-list-item
                              v-for="(item, index) in protocols"
                              :key="index"
                              @click="protocol = item"
                            >
                              <v-list-item-title>{{ item }}</v-list-item-title>
                            </v-list-item>
                          </v-list>
                        </v-menu>
                      </template>
                      <template v-slot:append>
                        <v-btn
                          v-if="status == 'connected'"
                          color="error"
                          dark
                          text
                          large
                          class="mt-n3 px-1"
                          @click="disconnect"
                        >
                          Disconnect
                        </v-btn>
                        <v-btn
                          v-else
                          color="primary"
                          dark
                          text
                          large
                          class="mt-n3 px-1"
                          :loading="status == 'connecting'"
                          @click="connect"
                        >
                          Connect
                        </v-btn>
                      </template>
                    </v-text-field>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-row v-for="message, index in messages" :key="index">
              <v-col>
                <message :message="message" ref="messages"></message>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
        <v-row v-if="status == 'connected'">
          <v-col>
            <new-message @send="this.send"></new-message>
          </v-col>
        </v-row>
      </v-container>
      <v-snackbar v-model="connected" color="success">Connected successfully.</v-snackbar>
      <v-snackbar v-model="disconnected" color="error">Connection lost!</v-snackbar>
    </v-main>
  </v-app>
</template>

<script>
import Message from './components/Message.vue'
import NewMessage from './components/NewMessage.vue';

export default {
  name: 'App',

  components: {
    Message,
    NewMessage,
  },

  data: () => ({
    url: 'echo.websocket.org',
    protocol: 'ws',
    status: 'disconnect',
    connected: false,
    disconnected: false,
    socket: undefined,
    protocols: [
      'ws',
      'wss'
    ],
    messages: [],
  }),

  mounted() {
    let config = localStorage.getItem('config')
    if (config) {
      let { protocol , url } = JSON.parse(config);
      this.protocol = protocol
      this.url = url
    }
  },

  methods: {
    connect() {
      let that = this
      this.status = "connecting"
      this.socket = new WebSocket(`${this.protocol}://${this.url}`);
      this.socket.addEventListener('open', function () {
        that.status = "connected"
        that.connected = true
      });

      this.socket.addEventListener('close', function () {
        that.status = "disconnect"
        that.disconnected = true
      });

      this.socket.addEventListener('message', this.message);
      localStorage.setItem('config', JSON.stringify({
        url: this.url,
        protocol: this.protocol,
      }))
    },
    disconnect() {
      this.status = "disconnect"
      if (this.socket) {
        this.socket.close()
      }
    },
    add(content, me) {
      this.messages.push({
        content: content,
        me: me,
        at: new Date
      })

      this.$nextTick(() => this.$vuetify.goTo(this.$refs.messages[this.$refs.messages.length-1]))
    },
    send(body) {
      this.socket.send(body)
      this.add(body, true)
    },
    message(event) {
      this.add(event.data, false)
    },
  }
};
</script>
