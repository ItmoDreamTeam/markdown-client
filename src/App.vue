<template>
  <div id="app">
    <controls></controls>
    <editor></editor>
  </div>
</template>

<script>
  import controls from '@/components/Controls.vue'
  import editor from '@/components/Editor.vue'

  export default {
    components: {
      controls,
      editor
    }
  }
</script>

<style>
  * {
    text-align: center;
  }
</style>
