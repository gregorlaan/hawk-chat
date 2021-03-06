<template>
  <div>
    <ul id="messages">
      <li class="message" v-for="message in messages" :key="message.id" @click="messageDetailsToggle(message.id)">
        <p class="details" v-if="messageDetails === message.id">{{ message.senderId }} <span>&bull;</span> {{ getUserPresence(message) }}</p>
        <p class="text">{{ message.text }}</p>
        <div class="name-tag" :class="{ 'current-user': message.senderId === currentUserId}">
          <p>{{ message.senderId[0] }}</p>
        </div>
      </li>
    </ul>
    <form id="send-message-area" @submit="submit($event)" autocomplete="off">
      <input placeholder="Send message" v-model="newMessage" type="text" name="newMessage" id="newMessage">
      <input id="send" value="" v-if="newMessage" type="submit">
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
    getUserPresence: function (message) {
      return message.userStore.presenceStore.store[message.senderId].state
    },
    messageDetailsToggle: function (id) {
      if (!this.messageDetails) {
        this.messageDetails = id
      } else if (this.messageDetails && this.messageDetails !== id) {
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
h1.title {
  color: white;
  text-shadow: 0 0 1px rgba(0, 0, 0, 0.3);
}

ul#messages {
  list-style: none;
  padding: 0;
  height: 100%;
}

#send-message-area {
  position: sticky;
  background: rgba(115, 219, 253, 0.9);
  bottom: 0;
  width: inherit;
  max-width: inherit;
}

input#newMessage {
  border: 0;
  box-sizing: border-box;
  width: 100%;
  padding: 20px;
  font-size: 16px;
  background: transparent;
  color: white;
  text-shadow: 0 0 1px rgba(0, 0, 0, 0.3);
}

input#newMessage::placeholder {
  color: white;
}

input#newMessage:focus {
  outline: 0;
}

#send {
  border: 0;
  position: absolute;
  right: 5px;
  background: 0;
  top: 0;
  bottom: 0;
  background-repeat: no-repeat;
  width: 50px;
  background-size: 15px;
  background-image: url(../assets/send-icon.svg);
  cursor: pointer;
  background-position: 50%;
}

ul#messages > li {
  display: flex;
  flex-wrap: wrap;
  margin: 20px;
}

ul#messages > li > p.text {
  flex: 1;
  display: flex;
  background: white;
  box-shadow: 1px 1px 5px 0 rgba(0, 0, 0, 0.4);
  border-radius: 25px;
  order: 1;
}

ul#messages > li > p.details {
  display: block;
  width: 100%;
  color: white;
  text-shadow: 0 0 1px rgba(0, 0, 0, 0.3);
}

ul#messages > li > .name-tag {
  text-transform: capitalize;
  flex: 0 0 40px;
  margin-right: 10px;
  text-align: center;
  justify-content: center;
  align-items: center;
  display: flex;
  order: 0;
}

ul#messages > li > .name-tag.current-user {
  order: 1;
  margin-left: 10px;
  margin-right: 0;
}

ul#messages > li > .name-tag > p {
  margin: 0;
  font-weight: bold;
  background: white;
  box-shadow: 1px 1px 5px 0 rgba(0, 0, 0, 0.4);
  border-radius: 100%;
  width: 40px;
  border-radius: 100%;
  padding: 10px 0;
}

ul#messages > li > p {
  margin: 0;
  padding: 10px;
}
</style>
