<template>
  <div>
    <div id="frame">
      <div id="sidepanel">
        <div id="profile">
          <div class="wrap">
            <img id="profile-img" src="http://emilcarlsson.se/assets/mikeross.png" class="online" alt=""/>
            <p>{{ name }}</p>
            <div id="expanded">
              <label><i class="fa fa-facebook fa-fw" aria-hidden="true"></i></label>
              <input name="twitter" type="text" value="mikeross" />
              <label><i class="fa fa-twitter fa-fw" aria-hidden="true"></i></label>
              <input name="twitter" type="text" value="ross81"/>
              <label><i class="fa fa-instagram fa-fw" aria-hidden="true"></i></label>
              <input name="twitter" type="text" value="mike.ross"/>
            </div>
          </div>
        </div>
        <div id="search">
          <label><i class="fa fa-search" aria-hidden="true"></i></label>
          <input type="text" placeholder="Search contacts..." v-model="txtSearch" @input="onChangeSearch"/>

          <div id="contacts">
            <ul>
              <div v-for="value in listSuggest" v-bind:key="value.Id">
                <li class="contact">
                  <div class="wrap">
                    <a role="button" v-on:click="selectChat(value.Name, value.Id)">
                      <div class="wrap">
                        <span class="contact-status online"></span>
                        <img src="http://emilcarlsson.se/assets/harveyspecter.png" alt=""/>
                        <div class="meta">
                          <p class="name">{{ value.Name }}</p>
                          <p class="preview">Việt Nam</p>
                        </div>
                      </div>
                    </a>
                  </div>
                </li>
              </div>
            </ul>

            <ul>
              <div v-for="(value, index) in listReceiver" v-bind:key="value.Id">
                <li class="contact">
                  <a role="button" v-on:click="selectChat(value.Name, value.Id)">
                    <div class="wrap">
                      <span class="contact-status online"></span>
                      <img src="http://emilcarlsson.se/assets/haroldgunderson.png" alt=""/>
                      <div class="meta">
                        <p class="name">{{ value.Name }}</p>
                        <p class="preview">Việt Nam</p>
                      </div>
                    </div>

                    <div v-if="listCountMessageNoRead[index] > 0">
                      <span class="notify-badge">{{ listCountMessageNoRead[index] }}</span>
                    </div>

                  </a>
                </li>
              </div>
            </ul>
          </div>
        </div>

        <!--        <div id="contacts">-->
        <!--          <ul>-->
        <!--            <div v-for="(value, key) in listReceiver">-->
        <!--              <li class="contact">-->
        <!--                <a role="button" v-on:click="selectChat(value.Name, value.Id)">-->
        <!--                  <div class="wrap">-->
        <!--                    <span class="contact-status online"></span>-->
        <!--                    <img src="http://emilcarlsson.se/assets/haroldgunderson.png" alt=""/>-->
        <!--                    <div class="meta">-->
        <!--                      <p class="name">{{ value.Name }}</p>-->
        <!--                      <p class="preview">Việt Nam</p>-->
        <!--                    </div>-->
        <!--                  </div>-->

        <!--                  <div v-if="listCountMessageNoRead[key] > 0">-->
        <!--                    <span class="notify-badge">{{ listCountMessageNoRead[key] }}</span>-->
        <!--                  </div>-->

        <!--                </a>-->
        <!--              </li>-->
        <!--            </div>-->
        <!--          </ul>-->
        <!--        </div>-->

        <div id="bottom-bar">
          <button id="addcontact"><i class="fa fa-user-plus fa-fw" aria-hidden="true"></i> <span>Add contact</span>
          </button>
          <button id="settings"><i class="fa fa-cog fa-fw" aria-hidden="true"></i> <span>Settings</span></button>
        </div>
      </div>

      <!--Content message chat-->
      <div class="content">
        <div v-if="nameChat !== '' ">
          <div class="contact-profile">
            <img src="http://emilcarlsson.se/assets/haroldgunderson.png" alt=""/>
            <p>{{ nameChat }}</p>
            <div class="social-media">
              <i class="fa fa-facebook" aria-hidden="true"></i>
              <i class="fa fa-twitter" aria-hidden="true"></i>
              <i class="fa fa-instagram" aria-hidden="true"></i>
            </div>
          </div>
        </div>

        <div class="messages">
          <ul>
            <div v-for="value in listMessage" v-bind:key="value.Id">

              <div v-if="value.SenderId !== parseInt(id)">
                <li class="replies">
                  <img src="http://emilcarlsson.se/assets/mikeross.png" alt=""/>
                  <p>{{ value.MessageContent }}</p>
                </li>
              </div>

              <div v-else>
                <li class="sent">
                  <img src="http://emilcarlsson.se/assets/haroldgunderson.png" alt=""/>
                  <p>{{ value.MessageContent }}</p>
                </li>
              </div>

            </div>
          </ul>
        </div>

        <div v-if="nameChat !== '' ">
          <div class="message-input">
            <div class="wrap">
              <input type="text" placeholder="Write your message..." v-model="txtMessage"/>
              <i class="fa fa-paperclip attachment" aria-hidden="true"></i>
              <button class="submit" v-on:click="submitSend"><i class="fa fa-paper-plane" aria-hidden="true"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'

export default {
  name: 'simplechat',
  data () {
    return {
      username: sessionStorage.getItem('username'),
      id: sessionStorage.getItem('id'),
      name: sessionStorage.getItem('name'),
      receiveId: '',
      listIdReceiver: [],
      listReceiver: [],
      listAccount: [],
      listSuggest: [],
      txtSearch: '',
      nameChat: '',
      listMessage: [],
      listCountMessageNoRead: [],
      txtMessage: ''
    }
  },
  mounted () {
    if (this.id !== null && this.id !== '') {
      this.getListId()
      this.getAccount()
    }
  },
  created () {
    if (this.id !== null && this.id !== '') {
      // this.getListId();
      // this.getAccount();

      try {
        setInterval(async () => {
          this.getAccount()
          this.refreshChat(this.id, this.receiveId)
        }, 10000)
      } catch (e) {
        console.log(e)
      }
    }
  },
  methods: {
    getListId () {
      axios.post('http://localhost:3000/api/message/get-receiver', {
        SenderId: parseInt(this.id)
      })
        .then((res) => {
          this.listIdReceiver = _.uniq(res.data)

          this.getListReceiver()
          this.getCountMessageNoRead()
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", 'error');
        })
    },

    getListReceiver () {
      axios.post('http://localhost:3000/api/user/get-by-list-id', {
        ListUserId: this.listIdReceiver
      })
        .then((res) => {
          this.listReceiver = _.sortBy(res.data, (v) => this.listIdReceiver.indexOf(v.id))
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", "error");
        })
    },

    getCountMessageNoRead () {
      axios.post('http://localhost:3000/api/message/count-message-no-read', {
        ListUserId: this.listIdReceiver,
        SenderId: parseInt(this.id)
      })
        .then((res) => {
          this.listCountMessageNoRead = res.data
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", "error");
        })
    },

    getAccount () {
      axios.get('http://localhost:3000/api/user', {})
        .then((res) => {
          this.listAccount = res.data
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", "error");
        })
    },

    refreshChat: function (SenderId, ReceiveId) {
      axios.post('http://localhost:3000/api/message/get-message', {
        SenderId: parseInt(SenderId),
        ReceiveId: parseInt(ReceiveId)
      })
        .then((res) => {
          this.listMessage = res.data

          this.getListId()
          this.getCountMessageNoRead()
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", "error");
        })
    },

    updateMessageNoRead: function (SenderId, ReceiveId) {
      axios.put('http://localhost:3000/api/message/update-message-no-read', {
        SenderId: parseInt(SenderId),
        ReceiveId: parseInt(ReceiveId)
      })
        .then((res) => {
          console.log(res.data.messages)
          // doing something
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", "error");
        })
    },

    submitSend () {
      axios.post('http://localhost:3000/api/message/add-message', {
        MessageChat: {
          MessageContent: this.txtMessage,
          IsRead: '0'
        },
        SenderReceive: {
          SenderId: parseInt(this.id),
          ReceiveId: parseInt(this.receiveId)
        }
      })
        .then((res) => {
          axios.post('http://localhost:3000/api/message/add-first-message', {
            MessageChat: {
              MessageContent: 'You will soon receive message reply',
              IsRead: '0'
            },
            SenderReceive: {
              SenderId: parseInt(this.receiveId),
              ReceiveId: parseInt(this.id)
            }
          })

          // Refresh chat message
          this.refreshChat(this.id, this.receiveId)
          this.txtMessage = ''
        })
        .catch((error) => {
          console.log(error.response.data.messages)
          // swal("Fail !", error.response.data.messages + " !", "error");
        })
    },

    onChangeSearch () {
      if (this.txtSearch === '') {
        this.listSuggest = []
      } else {
        var txt = this.txtSearch
        var listTemp = []

        _.forEach(this.listAccount, function (data, index) {
          if (_.includes(data.Name.toUpperCase(), txt.toUpperCase())) {
            listTemp.push(data)
          }
        })
        this.listSuggest = listTemp
      }
    },

    selectChat: function (name, id) {
      this.nameChat = name
      this.receiveId = id

      this.txtSearch = ''
      this.listSuggest = []

      this.refreshChat(sessionStorage.getItem('id'), id)
      this.updateMessageNoRead(this.id, id)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="css">
@import "../assets/css/message.css";
@import "http://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css";
@import "http://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css";
@import "https://cdnjs.cloudflare.com/ajax/libs/meyer-reset/2.0/reset.min.css";
@import "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.6.2/css/font-awesome.min.css";

</style>
