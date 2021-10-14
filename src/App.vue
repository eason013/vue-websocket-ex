<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view />
  </div>
</template>
<script>
export default {
  data() {
    return {
      websock: null,
    }
  },
  created() {
    this.initWebSocket();
  },
  destroyed() {
    this.websock.close() //離開路由之後斷開websocket連接
  },
  methods: {
    initWebSocket(){ //初始化weosocket
      const wsuri = "ws://127.0.0.1:8080";
      this.websock = new WebSocket(wsuri);
      this.websock.onmessage = this.websocketonmessage;
      this.websock.onopen = this.websocketonopen;
      this.websock.onerror = this.websocketonerror;
      this.websock.onclose = this.websocketclose;
      console.log(this.websock)
    },
    websocketonopen(){ //連接建立之後執行send方法發送數據
      let actions = {"eason":"hello server"};
      this.websocketsend(JSON.stringify(actions));
    },
    websocketonerror(){//連接建立失敗重連
      this.initWebSocket();
    },
    websocketonmessage(e){ //數據接收
      const redata = JSON.parse(e.data);
      console.log(redata)
    },
    websocketsend(Data){//數據發送
      this.websock.send(Data);
    },
    websocketclose(e){  //關閉
      console.log('斷開連接',e);
    },  
  }  
}
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
