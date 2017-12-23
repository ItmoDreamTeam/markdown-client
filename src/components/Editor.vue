<template>
  <div id="editor_component">
    <div id="editor">
      <textarea id="area" v-model="content"></textarea>
      <div class="panel">
        <button v-on:click="saveMd">Save</button>
        <button v-on:click="deleteMd">Delete</button>
      </div>
    </div>
    <div id="viewer" v-html="getMd"></div>
  </div>
</template>

<script>
  import $ from 'jquery'
  import marked from 'marked'
  import constants from '@/constants'
  import controls from '@/components/Controls.vue'

  export default {
    data: function () {
      return {content: ''}
    },

    methods: {
      saveMd: function () {
        if (controls.selected === undefined) return
        console.log('saving ' + controls.selected)
        $.ajax({
          url: constants.apiRoot() + '/api/md/' + controls.selected,
          method: 'PUT',
          data: {
            'content': this.content
          }
        }).done(function () {
          controls.refresh()
        }).fail(function (error) {
          alert(error.responseText)
        })
      },

      deleteMd: function () {
        if (controls.selected === undefined) return
        console.log('deleting ' + controls.selected)
        $.ajax({
          url: constants.apiRoot() + '/api/md/' + controls.selected,
          method: 'DELETE'
        }).done(function () {
          controls.selected = ''
          this.content = ''
          controls.refresh()
        }).fail(function (error) {
          alert(error.responseText)
        })
      }
    },

    computed: {
      getMd: function () {
        return marked(this.content)
      }
    }
  }
</script>

<style scoped>
  #editor_component {
    display: inline-block;
    vertical-align: center;
  }

  #editor {
    vertical-align: top;
    display: inline-block;
    margin: 0 20px 20px 20px;
  }

  #area {
    min-width: 400px;
    min-height: 500px;
    padding: 5px;
  }

  #viewer {
    vertical-align: top;
    display: inline-block;
    border: solid green 1px;
    min-width: 400px;
    min-height: 504px;
    text-align: left;
    padding: 5px;
  }
</style>
