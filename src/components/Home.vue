<template>
  <div>
    <h1>Hawk Chat</h1>
  </div>
</template>

<script>
import { ChatManager, TokenProvider } from '@pusher/chatkit'

export default {
  data () {
    return {}
  },
  methods: {
    onLoad: function () {
      console.log('App loaded.')
      const chatManager = new ChatManager({
        instanceLocator: 'v1:us1:811af568-f6dc-4eb4-bed1-c842ac6cdb78',
        userId: 'gregor.laan',
        tokenProvider: new TokenProvider({ url: 'https://us1.pusherplatform.io/services/chatkit_token_provider/v1/811af568-f6dc-4eb4-bed1-c842ac6cdb78/token' })
      })

      chatManager.connect()
        .then(currentUser => {
          console.log('Successful connection', currentUser)
        })
        .catch(err => {
          console.log('Error on connection', err)
        })
    }
  },
  mounted: function () {
    this.onLoad()
  }
}
</script>

<style scoped>
</style>
