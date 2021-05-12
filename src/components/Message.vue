<template>
  <div>
    <v-card :color="message.me ? 'primary' : ''" :dark="message.me">
      <v-card-title dense class="pa-1">
        <v-spacer />
        <v-btn icon small fab left top @click="copy">
          <v-icon small>
            mdi-content-copy
          </v-icon>
        </v-btn>
      </v-card-title>
      <v-card-text class="mt-n10 content">
        <pre ref="message">{{data}}</pre>
      </v-card-text>
      <v-card-actions class="mt-n10">
        <v-spacer />
        <small>{{time}}</small>
      </v-card-actions>
    </v-card>
    
  </div>
</template>

<script>
export default {
  name: 'Message',
  props: {
    message: Object,
  },
  data: () => ({
    data: undefined,
    time: '',
  }),
  methods: {
    copy() {
      navigator.clipboard.writeText(this.message.content);
    },
    pad(i) {
      return i < 10 ? "0" + i : i
    },
  },
  mounted() {
    this.time = this.message.at.getHours() + ":" + this.pad(this.message.at.getMinutes())

    try {
      this.data = JSON.stringify(JSON.parse(this.message.content), null, 2)
    }
    catch(e) {
      this.data = this.message.content
    }
  },
}
</script>

<style scoped>
pre {
  font-size: 1.1em;
}
.content {
  min-height: 70px;
}
</style>