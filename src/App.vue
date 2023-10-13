<template>

  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css">

  <div class="black-bg" v-if="flag==true">
    <div class="white-bg">
      <h4>상세 페이지</h4>
      <p>상세 페이지 내용</p>
      <button @click="flag=false">닫기</button>
    </div>
  </div>
  
  <div>
    <router-link to="/" class="header-link">
    <h1>KCS2</h1>
    <h4>Kubernetes Client Scanner Service</h4>
    </router-link>
  </div>
  <div>
    <h4 style="margin-right: 5px;">URL</h4>
    <p><input style="width: 30%; height: 30px; font-size: 15px;" v-model="url" placeholder="URL을 입력하세요"></p>
    <h4 style="margin-right: 5px;">Token</h4>
    <p><input type= "password" style="width: 30%; height: 30px; font-size: 15px;" v-model="token" placeholder="Token을 입력하세요"></p>
    <button type="button" class="btn btn-primary" @click="sendDataToBackend">Primary</button>
    <div class="container"><div class="text-box">
      <p v-for="message in logMessage" :key="message">{{ message }}</p>
      <p v-if="logMessage.length === 0">No log messages available.</p>
    </div></div>
  </div>
</template>

<script>
//import HomeButton from './components/HomeButton.vue'; // Import the HomeButton component
import axios from 'axios';


export default {
  name: 'App',
  data(){
    return {
      inputText : "각각의 NODE에 대한 LOG",
      logMessage : [],
      flag : false,
      url : '',
      token : '',
    }
  },
  methods: {
    sendDataToBackend() {
        // Data to send to the backend
        const data = { token: this.token, url: this.url };
        axios.post('http://localhost/8080/api/runCurl', data)
          .then(response => {
            // Handle the response from the backend
            this.logMessage = response.data;
          })
          .catch(error => {
            // Handle any errors
            console.error(error);
          });
      },
  },
  components: {
    //'home-button': HomeButton // Register the HomeButton component
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

  width: 1000px;
  height: 400px;
}
.container {
  display: flex;
  justify-content: center; /* Center horizontally */
  align-items: center; /* Center vertically */
  height: 50vh; /* Optional: Make the container full height of the viewport */
}
.header-link h1,
.header-link h4 {
  display: inline;
  margin-right: 15px;
}
a {
  color: inherit; /* Use the default text color */
  text-decoration: none; /* Remove underline */
}
</style>