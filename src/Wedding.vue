<template>
  <div class="wedding">
    <Editor />
  </div>
</template>

<script>
import Editor from "./components/Editor.vue";

export default {
  props: [],
  components: {
    Editor,
  },
  name: "Wedding",
};
</script>

<style lang="less">
@import "./assets/base.less";
html,
body {
  height: 100%;
}
.wedding {
  position: fixed;
  width: 100%;
  max-width: 750px !important;
  height: 100%;
  background: #2b2b48;
  perspective: 500px;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
</style>
