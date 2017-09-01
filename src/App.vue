<template>
  <div id="app">
    <img src="./assets/logo.png">
    
    <Vueditor v-model="cont" :content='cont' @on-content-change='change' >
    </Vueditor>
  </div>
</template>

<script>
import Vueditor from './components/Vueditor';

export default {
  name: 'app',
  data() {
    return {
      cont: '<p>hello vue ueditor </p>',
    };
  },
  methods: {
    change(val) {
      this.cont = val;
      console.log(`编辑器内容Change：${val}`);
    },
    editorReady(editorInstance) {
      editorInstance.addListener('contentChange', () => {
        console.log(`编辑器内容：${editorInstance.getContent()}`);
      });
    },
  },
  components: {
    Vueditor,
  },
};
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
