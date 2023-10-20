
<template>
  <head>
    <title>WebSocket Example</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/sockjs-client/1.5.2/sockjs.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/stomp.js/2.3.3/stomp.min.js"></script>
  </head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css">
  <div class="black-bg" v-if="isLoading==true"><div class="container">
  <div class="spinner" style="margin-top: 250px;"></div>
  </div></div>


    <div class="black-bg" v-if="flag==true">
    <div class="container"><div class="text-box">
      <p v-for="message in logMessage" :key="message" v-html="formatLogMessage(message)"></p>
    </div></div><button type="button" class="btn btn-primary" style="margin-top: 10px;" @click="flag=false">닫기</button></div>
    
  <div>
    <router-link to="/" class="header-link">
    <h1>KCS2</h1>
    <h4>Kubernetes Client Scanner Service</h4>
    </router-link>
  </div>
  <div>
    <h4 style="margin-right: 5px; margin-top: 100px;">URL</h4>
    <p><input type="text" style="width: 30%; height: 30px; font-size: 15px;" v-model="apiServer" autocomplete="off" placeholder="URL을 입력하세요"></p>
    <h4 style="margin-right: 5px;">Token</h4>
    <p><input type= "password" style="width: 30%; height: 30px; font-size: 15px;" v-model="token" placeholder="Token을 입력하세요"></p>
    <button type="button" class="btn btn-primary" @click="sendDataToBackend">Primary</button>

  </div>
</template>

<script>
//import HomeButton from './components/HomeButton.vue'; // Import the HomeButton component
import axios from 'axios'

export default {
  name: 'App',
  data(){
    return {
      inputText : "각각의 NODE에 대한 LOG",
      logMessage : [],
      stompClient: null,
      flag : false,
      isLoading : false,
      apiServer : '',
      token : '',
    }
  },
  methods: {
    pushMessage(message) {
      this.logMessage.push(message);
    },
    formatLogMessage(rawLog) {
      // Process the log message here, such as adding line breaks or other formatting
      const formattedLog = rawLog.replace(/\n/g, '<br>'); // Replace line breaks with <br> tags
      return formattedLog;
    },
    sendDataToBackend() {
      const messageData = {
        apiServer: this.apiServer,
        token: this.token,
      }
      this.stompClient.send("/app/chat.sendDataToBackend", JSON.stringify({messageData}))
      this.apiServer = ""; this.token = ""
    },
    connectWebSocket() {
      var socket = new SocketJS('/ws-chat');
      this.stompClient = Stomp.over(socket);

      this.stompClient.connect({}, (frame)=>{
        this.stompClient.subscribe('/topic/publicChat', (response)=>{
          var message = JSON.parse(response.body).connect;
          this.showMessage(message)
        });
      });
    },
  }, 
  mounted() {
    this.connectWebSocket();
  },
  components: {
    //'home-button': HomeButton // Register the HomeButton 
  },
}
</script>

<style>
body{
  margin : 0
}
div {
  box-sizing: border-box;
}
.black-bg{
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.5);
  position: fixed; padding: 20px;
}
.white-bg{
  width: 100%; background: white;
  border-radius: 8px;
  padding: 20px;
}

.room-img{
  width: 30%;
  margin-top: 40px;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

.menu {
  background: darkslateblue;
  padding: 15px;
  border-radius: 5px;
}
.menu a {
  color: white;
  padding: 10px;
}
.text-box {
  border: 1px solid #ccc; /* Add a border */
  padding: 10px; /* Add some padding */
  background-color: #f0f0f0; /* Set a background color */
  border-radius: 5px; /* Add rounded corners */
  margin-top: 10px; /* Adjust margin for spacing */

  width: 1300px;
  height: 600px;
  
  overflow-y: scroll;
  overflow-x: hidden;

  text-align: left;
  white-space: pre-line;
}
.container {
  display: flex;
  justify-content: center; /* Center horizontally */
  align-items: center; /* Center vertically */
  /* height: 50vh;  Optional: Make the container full height of the viewport */
}
.header-link h1,
.header-link h4 {
  display: inline;
  margin-right: 15px;
}
.spinner {
    border: 8px solid rgba(0, 0, 0, 0.1);
    border-left: 8px solid #3498db;
    border-radius: 100%;
    width: 80px;
    height: 80px;
    animation: spin 2s linear infinite;
  }

  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
a {
  color: inherit; /* Use the default text color */
  text-decoration: none; /* Remove underline */
}
</style>
