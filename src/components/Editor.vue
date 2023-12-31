<template>
  <div class="wedding-editor" ref="editor">
    <header class="editor-header">
      <a href="javascript:;"></a>
      <a href="javascript:;" class="minimum"></a>
      <a href="javascript:;" class="maximum"></a>
    </header>
    <!-- 日期 -->
    <p class="code">
      Last login: <span>{{ startDate }}</span> on ttys001
    </p>
    <!--代码编辑区-->
    <pre>
      <code v-html="highlightedCode"></code>
    </pre>
    <Executions
      :canExecute="canExecute"
      @onUpdating="scrollToBottom"
      @onFinish="canOpen = true"
    />
    <invitation :canOpen="canOpen" @sendBarrage="onAfterSending" />
    <Barrage :wish="wish" :canStart="canStart" />
  </div>
</template>

<script>
import Prism from "prismjs";
import "prismjs/themes/prism-okaidia.css";
import "../utils/raf";
import data from "../mock/data";

import Executions from "./Executions";
import Invitation from "./Invitation";
import Barrage from "./Barrage";

export default {
  name: "Editor",
  components: { Executions, Invitation, Barrage },
  data() {
    return {
      startDate: "",
      code: data.code,
      currentCode: "",
      isCursorVisible: 1,
      canExecute: false,
      canOpen: false,
      wish: "",
      canStart: false,
    };
  },
  created() {
    this.startDate = new Date().toDateString();
    this.progressivelyTyping();
  },
  updated() {
    this.scrollToBottom();
  },
  computed: {
    highlightedCode() {
      const code = Prism.highlight(
        this.currentCode,
        Prism.languages.javascript
      );
      const codeWithCursor = `${code}<span style="opacity:${this.isCursorVisible}"> ▍</span>`;
      return codeWithCursor;
    },
  },
  methods: {
    scrollToBottom() {
      // 保持页面一直滚到最下面
      this.$refs.editor.scrollTop = 100000;
    },
    // 代码输入
    progressivelyTyping() {
      return new Promise((resolve) => {
        let count = 0,
          typingCount = 0,
          typing;
        // 写代码每一帧的函数
        let step = () => {
          let randomNumber = Math.round(Math.random() * 6);
          // 摸你打字的随机速度
          if (count % 2 === 0 && randomNumber % 4 === 0) {
            this.currentCode = this.code.substring(0, typingCount);
            typingCount++;
          }
          // 大约每 24 帧光标闪动一次
          if (count % 24 === 0) {
            this.isCursorVisible = this.isCursorVisible === 0 ? 1 : 0;
          }
          count++;
          if (typingCount <= this.code.length) {
            typing = requestAnimationFrame(step);
          } else {
            resolve();
            setTimeout(() => {
              this.canExecute = true;
            }, 500);
            cancelAnimationFrame(typing);
          }
        };
        typing = requestAnimationFrame(step);
      });
    },
    // 发送弹幕之后
    onAfterSending(wish) {
      this.wish = wish;
      this.canOpen = false;
      setTimeout(() => {
        this.canStart = true;
      }, 800);
    },
  },
};
</script>

<style lang="less">
.wedding-editor {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  padding: 0.8rem 0.24rem 0.24rem;
  overflow-x: hidden;
  overflow-y: auto;
  z-index: 1;
  transform-origin: 0 0;
  -webkit-transform-origin: 0 0;
  transition: all 1.6s cubic-bezier(0.4, 0, 1, 1);
  -webkit-transition: all 1.6s cubic-bezier(0.4, 0, 1, 1);
  .editor-header {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    padding: 0.24rem;
    overflow: hidden;
    background: #2b2b48;
    z-index: 3;
    > a {
      float: left;
      display: block;
      width: 0.32rem;
      height: 0.32rem;
      margin-right: 0.1rem;
      border-radius: 0.16rem;
      background: #fc615d;
      &.minimum {
        background: #fdbc40;
      }
      &.maximum {
        background: #34c84a;
      }
    }
  }
  p.code {
    margin: 0;
    color: #bbb;
    line-height: 1.5;
    font-family: "Roboto Mono", "Menlo", "Monaco", Courier, monospace !important;
    font-weight: 500 !important;
    font-size: 0.26rem !important;
  }
  pre {
    margin: 0;
    white-space: pre-wrap;
    code {
      white-space: pre-wrap;
      word-break: break-all;
      font-size: 0.26rem !important;
      margin: 0;
      color: #bbb;
      line-height: 1.5;
      font-family: "Roboto Mono", "Menlo", "Monaco", Courier, monospace !important;
      font-weight: 500 !important;
      background: transparent;
    }
  }
}
</style>