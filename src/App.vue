<template>
  <div id="app">
    <fancy-box-vue></fancy-box-vue>
    <word-list></word-list>
  </div>
</template>


<script>

  import wordList from "./components/wordList.vue"
  import fancyBoxVue from "./components/fancyBox.vue"
  export default {
    name: 'App',
    components : {
      wordList,
      fancyBoxVue
    }
  }
</script>