<template>
  <v-card>
    <v-card-title class="py-2">
      Send a new Message
    </v-card-title>
    <v-divider />
    <v-card-text>
      <v-textarea
        label="Body"
        outlined
        hide-details
        v-model="body"
        @keyup.enter="enter"
      ></v-textarea>
    </v-card-text>
    <v-card-actions>
      <small>Press alt+enter to send.</small>
      <v-spacer />
      <v-btn color="primary" @click="$emit('send', body)">Send</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: 'NewMessage',
  data: () => ({
    body: "",
  }),
  methods: {
    enter(event) {
      if (event.altKey || event.ctrlKey) {
        this.$emit('send', this.body)
      }
    }
  }
}
</script>