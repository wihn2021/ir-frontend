<script>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import ACControl from './components/ACControl.vue'
import ChooseBrand from './components/ChooseBrand.vue'
import {appKey, appSecret} from './config.js'

export default {
  data() {
    return {
      modes: {
        listDevice: true,
        choose: false,
        control: false,
      },
      uid: null,
      token: null,
      currentDevice: {
        mac: null,
        indexId: null,
        name: null,
      },
      list: null
    }
  },
  methods: {
    gotoChoose(d) {
      this.currentDevice.mac = d["mac"]
      this.currentDevice.name = d["name"]
      this.modes.choose = true
      this.modes.listDevice = false
      this.modes.control = false
    },
    gotoControl(d) {
      this.currentDevice.mac = d["mac"]
      this.currentDevice.name = d["name"]
      this.currentDevice.indexId = d["indexId"]
      this.modes.choose = false
      this.modes.listDevice = false
      this.modes.control = true
    }
  },
  mounted() {
    fetch("/irext-server/app/app_login", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(
          {
            "appKey": appKey,
            "appSecret": appSecret,
            "appType": "2",
          }),
    }).then(resp => resp.json()).then(res => {
      this.uid = res["entity"]["id"]
      this.token = res["entity"]["token"]
    })
    fetch("/deviceList", {
      method: "GET",
    }).then(resp => resp.json()).then(res => {
      this.list = res
    })
  },
  components: {
    ACControl,
    ChooseBrand
  },
}
</script>

<template>
  <div id="devicelist" v-if="modes.listDevice">
    <div id="list" v-if="list">
      <div v-for="d in list">
        <div>
          <h1>
            {{ d["name"] }}
          </h1>
          <h2>
            {{ d["mac"] }}
          </h2>
          <p>温度: {{ d["temperature"] }}°C 湿度: {{ d["humidity"] }}%</p>
          <button @click="gotoChoose(d)">Edit</button>
          <button @click="gotoControl(d)">Control</button>
        </div>
      </div>
    </div>
    <div v-else>
      <h1>
        loading . . . . . .
      </h1>
    </div>
  </div>
  <div id="choose" v-else-if="modes.choose">
    <ChooseBrand :uid="uid" :token="token" :mac="currentDevice.mac"/>
  </div>
  <div id="control" v-else>
    <ACControl :uid="uid" :token="token" :indexId="currentDevice.indexId" :mac="currentDevice.mac"/>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
