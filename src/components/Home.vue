<template>
  <div>
    <h1>Hawk Chat</h1>
    <ul>
      <li v-for="message in messages" :key="message.id">
        <p>{{ message.senderId }}</p>
        <p>{{ message.text }}</p>
      </li>
    </ul>
    <form @submit="submit($event)">
      <input v-model="newMessage" type="text" name="newMessage" id="newMessage2">
      <input v-if="newMessage" type="submit">
      <input v-if="newMessage" type="reset">
    </form>
  </div>
</template>

<script>
import { ChatManager, TokenProvider } from '@pusher/chatkit'

export default {
  data () {
    return {
      messages: [],
      newMessage: '',
      roomId: 12724024
    }
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
          this.connectToRoom(currentUser)
          this.getMessages(currentUser)
          this.submit = function (e) {
            this.sendMessage(e, currentUser)
          }
        })
        .catch(err => {
          console.log('Error on connection', err)
        })
    },
    sendMessage: function (e, user) {
      e.preventDefault()
      console.log('New Message: ' + this.newMessage)
      user.sendMessage({
        text: this.newMessage,
        roomId: this.roomId
      })
    },
    getMessages: function (user) {
      user.fetchMessages({
        roomId: this.roomId,
        direction: 'older',
        limit: 10
      })
        .then(messages => {
          console.log(messages)
          this.messages = messages
        })
        .catch(err => {
          console.log(`Error fetching messages: ${err}`)
        })
    },
    connectToRoom: function (user) {
      user.subscribeToRoom({
        roomId: this.roomId,
        hooks: {
          onNewMessage: message => {
            console.log(`Received new message: ${message.text}`)
            this.messages.push(message)
          }
        }
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
