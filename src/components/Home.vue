<template>
  <div>
    <h1>Hawk Chat</h1>
    <ul id="messages">
      <li class="message" v-for="message in messages" :key="message.id" @click="messageDetailsToggle(message.id)">
        <p class="details" v-if="messageDetails === message.id">{{ message.senderId }}</p>
        <p class="text">{{ message.text }}</p>
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
      messageDetails: false,
      roomId: 12724024,
      currentUserId: 'gregor.laan'
    }
  },
  methods: {
    onLoad: function () {
      console.log('App loaded.')
      const chatManager = new ChatManager({
        instanceLocator: 'v1:us1:811af568-f6dc-4eb4-bed1-c842ac6cdb78',
        userId: this.currentUserId,
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
      this.newMessage = ''
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
    },
    messageDetailsToggle: function (id) {
      if (!this.messageDetails) {
        this.messageDetails = id
      } else if (this.messageDetails) {
        this.messageDetails = false
      }
    }
  },
  mounted: function () {
    this.onLoad()
  }
}
</script>

<style scoped>
ul#messages {
  list-style: none;
  padding: 0;
  overflow-y: scroll;
}

ul#messages > li {
  background: white;
  margin: 10px;
  box-shadow: 1px 1px 5px 0 rgba(0, 0, 0, 0.4);
  border-radius: 3px;
}

ul#messages > li > p {
  margin: 0;
  padding: 5px;
}
</style>
