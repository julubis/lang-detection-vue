<template>
  <div id="app">
    <section class="hero is-large">

      <div class="hero-head">
        <nav class="navbar">
          <div class="container">
            <div class="navbar-brand">
              <a class="navbar-item">
                <p class="title">Language Identification</p>
              </a>
            </div>
          </div>
        </nav>
      </div>

      <div class="hero-body">
        <div class="container has-text-centered">
          <div class="columns">
            <div class="column"></div>
            <div class="column is-two-thirds">
              <div class="box">
                <b-tag v-if="text.trim()" type="is-primary" size="is-large">
                  <template v-if="!onTranslate && isConnect">{{lang}} Language</template>
                  <template v-else>Loading...</template>
                </b-tag>
                <b-tag v-else type="is-primary" size="is-large">Input Text</b-tag>
                <b-field class="mt-3 mb-3">
                  <b-input v-on:input="tampilkan" v-model="text" type="textarea" :disabled="!isConnect"></b-input>
                </b-field>
              </div>
            </div>
            <div class="column"></div>
          </div>
          <p class="subtitle">
            Support 22 languages
          </p>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      text: '',
      lang: '',
      onTranslate: false,
      socket: null,
      isConnect: false
    }
  },
  methods: {
    tampilkan() {
      if (this.text.trim() !== '') {
        this.onTranslate = true
        this.socket.send(this.text)
      }
    },
    connect() {
      this.socket = new WebSocket('wss://lang.aiproject1.repl.co/identify')
      this.socket.onopen = (e) => {
        this.isConnect = true
        this.$buefy.toast.open({
          message: "Connected",
          type: "is-success"
        })
      }
      this.socket.onmessage = (e) => {
        this.lang = e.data
        this.onTranslate = false
      }
      this.socket.onerror = (e) => {
        this.isConnect = false
        this.$buefy.toast.open({
          message: "Disconnected",
          type: "is-danger"
        })
        setTimeout(() => this.connect(), 2000)
      }
      this.socket.oneclose = (e) => {
        this.isConnect = false
        this.$buefy.toast.open({
          message: "Disconnected",
          type: "is-danger"
        })
        setTimeout(() => this.connect(), 2000)
      }
    }
  },
  created() {
    this.connect()
  }
}
</script>
