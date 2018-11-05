<template>
  <div id="app">
    <input type="text" v-model="host">
    <button @click="connect">連線</button><br><br>
    <label>監聽<input type="text" placeholder="channel name" v-model="channel">頻道</label><br>
    <label>的<input type="text" placeholder="event name" v-model="event">事件</label>
    <button @click="join">監聽</button>
    <button @click="joinPrivate">監聽私有頻道</button>
    <br>
    <div>{{content}}</div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import Echo from 'laravel-echo'
import io from 'socket.io-client'

@Component
export default class App extends Vue {
  echo!: Echo
  host: string = 'localhost:6001'
  channel: string = ''
  event: string = ''
  content: string = ''

  beforeMouted () {
    (<any>window).io = io
  }

  connect () {
    try {
      this.echo = new Echo({
        broadcaster: 'socket.io',
        host: this.host
      })
      this.content = '連線成功'
    } catch (e) {
      this.content = '連線失敗'+'\n'+e.toString()
    }
  }

  join () {
    this.echo!.channel(this.channel).listen(this.event, (e: any) => {
      this.content += '\n'
      this.channel += e.toString()
    })
  }

  joinPrivate () {
    this.echo!.private(this.channel).listen(this.event, (e: any) => {
      this.content += '\n'
      this.channel += e.toString()
    })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
