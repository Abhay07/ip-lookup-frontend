<template>
  <div id="app">
    <img src="./assets/myips.jpg" alt="FInd IP">
    <h2>Your IPv4 address is <span class="myip">{{userIp}}</span></h2>
    <div>
      <h2 class="search-label">Search for other IP</h2>
      <div class="row">
        <input type="text" placeholder="Enter IP" v-model="inputIp">
        <button @click="searchIp" v-model="userIp">GET IP LOCATION</button>
      </div>
    </div>
    <div v-if="lookupIPLocation">
      <h2>IP : <span class="myip">{{lookupIP}}</span></h2>
      <h2>Location : <span class="myip">{{lookupIPLocation}}</span></h2>
    </div>
    <div class="alert" v-show="alertVisible">
      <span>{{alertMsg}}</span>
      <span @click="hideAlert" class="cross">x</span>
    </div>
    <div class="loader" v-show="loaderVisible">
      <div class="lds-ring"><div></div><div></div><div></div><div></div></div>
    </div>
  </div>
</template>

<script>
import {API_ENDPOINT, DEFAULT_ERROR_MSG, IP_REGEX, INVALID_IP_MSG, MY_IP_API_ENDPOINT} from './constants';
export default {
  name: 'App',
  data:()=>({
    userIp:null,
    alertVisible:false,
    loaderVisible:true,
    lookupIP:null,
    lookupIPLocation:null,
    inputIp:null,
    alertMsg:null
  }),
  created(){
    fetch(API_ENDPOINT)
    .then(res=>res.text())
    .then(res=>this.userIp = res)
    .catch(err=>{
      this.showAlert();
    })
    .finally(()=>{
      this.loaderVisible = false;
    })
  },
  methods:{
    showAlert(msg){
      if(msg){
        this.alertMsg = msg
      }
      else{
        this.alertMsg = DEFAULT_ERROR_MSG;
      }
      this.alertVisible = true;
    },
    hideAlert(){
      this.alertVisible = false;
    },
    searchIp(){
      const ip = this.inputIp && this.inputIp.trim();
      if(!ip.match(IP_REGEX)){
        return this.showAlert(INVALID_IP_MSG);
      }
      this.loaderVisible = true;
      fetch(`${API_ENDPOINT}/?ip=${ip}`)
      .then(res=>res.json())
      .then(res=>{
        let location = "";
        if(res.city){
          location+=`${res.city}`
        }
        if(res.region_name){
          location+=`, ${res.region_name}`;
        }
        if(res.country_name){
          this.lookupIP = res.ip;
          location+=`, ${res.country_name}`;
        }
        this.lookupIPLocation = location;
      })
      .catch(err=>{
        this.showAlert();
      })
      .finally(()=>{
        this.loaderVisible = false;
      })
    }
  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.row{
  display: flex;
}

input{
  padding: 20px;
  width:100%;
  font-size: 20px;
}
button{
  padding: 20px;
  border:none;
  color:#fff;
  background-color: blue;
  border-radius: 2px;
  cursor: pointer;
}
button:hover{
  background-color: red;
}

.alert{
  color:#fff;
  position: fixed;
  top:20px;
  background-color:red;
  border-radius: 2px;
  display: flex;
  justify-content: space-between;
}

.alert span{
  padding: 5px 10px;
}

.alert .cross{
  display: inline-block;
  cursor: pointer;
}

.alert .cross:hover{
  background-color: green;
}

.myip{
  color:blue;
}
.search-label{
  text-align: left;
  margin-bottom: 5px;
}
.loader{
  position: fixed;
  top:0;
  left: 0;
  width:100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color:rgba(0,0,0,0.2);
}

.lds-ring {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.lds-ring div {
  box-sizing: border-box;
  display: block;
  position: absolute;
  width: 64px;
  height: 64px;
  margin: 8px;
  border: 8px solid #fff;
  border-radius: 50%;
  animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  border-color: #fff transparent transparent transparent;
}
.lds-ring div:nth-child(1) {
  animation-delay: -0.45s;
}
.lds-ring div:nth-child(2) {
  animation-delay: -0.3s;
}
.lds-ring div:nth-child(3) {
  animation-delay: -0.15s;
}
@keyframes lds-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

</style>
