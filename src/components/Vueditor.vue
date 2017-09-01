<template>
  <script :id='id' ref="vueditor" type="text/plain">
  </script>
</template>

<script>
export default {
  name: 'vue-ueditor',
  props: {
    ueditorPath: {
      type: String,
      required: false,
      default: '/static/ueditor/',
    },
    content: {
      type: String,
    },
    disabled: {
      type: Boolean,
      required: false,
    },
    options: {
      type: Object,
      required: false,
      default: () => { },
    },
  },
  data() {
    return {
      id: `vueditor-${Math.random().toString(10).replace('0.', '')}`,
      vcontent: '',
      instance: null,
      defaultConfig: {
        toolbars: [
          ['fullscreen', 'source', 'undo', 'redo', 'bold'],
        ],
        autoHeightEnabled: true,
        autoFloatEnabled: true,
      },
    };
  },
  mounted() {
    this.initialize();
  },
  beforeDestroy() {
    if (this.instance !== null && this.instance.destroy) {
      this.instance.destroy();
      this.instance = null;
    }
  },
  methods: {
    initialize() {
      if (this.$el) {
        // let self = this
        this.createHeadElementAndLoadJs();
      }
    },
    createHeadElementAndLoadJs() {
      let configScriptElement = document.getElementById('ueditor-config-javascript');
      let ueditorScriptElement = document.getElementById('ueditor-all-javascript');
      let langScriptElement = document.getElementById('ueditor-lang-javascript');
      // let langLinkElement = document.getElementById('ueditor-css-link');
      // ueditor.config.js
      if (configScriptElement === null) {
        configScriptElement = document.createElement('script');
        configScriptElement.id = 'ueditor-config-javascript';
        configScriptElement.type = 'text/javascript';
        configScriptElement.src = `${this.ueditorPath}ueditor.config.js`;
      }

      // ueditor.all.js
      if (ueditorScriptElement === null) {
        ueditorScriptElement = document.createElement('script');
        ueditorScriptElement.id = 'ueditor-all-javascript';
        ueditorScriptElement.type = 'text/javascript';
        ueditorScriptElement.src = `${this.ueditorPath}ueditor.all.js`;
      }
      if (langScriptElement === null) {
        langScriptElement = document.createElement('script');
        langScriptElement.id = 'ueditor-lang-javascript';
        langScriptElement.type = 'text/javascript';
        langScriptElement.src = `${this.ueditorPath}/lang/zh-cn/zh-cn.js`;
      }
      console.log(ueditorScriptElement);
      const s = document.getElementsByTagName('head')[0];
      s.appendChild(configScriptElement);
      configScriptElement.onload = () => {
        s.appendChild(ueditorScriptElement);
        ueditorScriptElement.onload = () => {
          this.initUeditorView();
        };
      };
    },
    initUeditorView() {
      this.vcontent = this.content;
      if (this.instance === null) {
        this.$nextTick(() => {
          const self = this;
          if (self.options) {
            self.instance = window.UE.getEditor(self.id, self.options.config);
          } else {
            self.instance = window.UE.getEditor(self.id, self.defaultConfig);
          }
          // contentChange
          self.instance.on('contentChange', () => {
            self.vcontent = self.instance.getContent();
            self.$emit('on-content-change', self.vcontent);
          });

          // emit ready
          self.$emit('ready', self.instance);
        });
      }
    },
  },
};
</script>