<template>
  <div id="main_page">
    <div id="controls">
      <div id="add" class="list-item">
        <input placeholder="Name" v-model="mdName">
        <button v-on:click="createMd">Add</button>
      </div>
      <div>
        <div class="list-item" v-for="md in mds" v-on:click="selectMd(md)">
          <div v-if="md._id === selected" style="background-color: aqua">{{md._id}}</div>
          <div v-else>{{md._id}}</div>
        </div>
      </div>
    </div>
    <div id="editor_component">
      <div id="editor">
        <textarea id="area" v-model="content"></textarea>
        <div>
          <button v-on:click="saveMd">Save</button>
          <button v-on:click="deleteMd">Delete</button>
        </div>
      </div>
      <div id="viewer" v-html="getMd"></div>
    </div>
  </div>
</template>

<script>
  import $ from 'jquery'
  import marked from 'marked'
  import constants from '@/constants'

  export default {
    data: function () {
      return {
        mdName: '',
        selected: '',
        mds: [],
        content: ''
      }
    },

    methods: {
      refresh: function () {
        console.log('refreshing')
        this.mdName = ''
        const self = this
        $.get(constants.apiRoot() + '/api/md', function (response, status) {
          self.mds = response.reverse()
        })
      },

      selectMd: function (md) {
        this.selected = md._id
        this.content = md.content
      },

      createMd: function () {
        this.mdName = this.mdName.trim()
        if (this.mdName === '') return
        console.log('creating ' + this.mdName)
        const self = this
        $.post(constants.apiRoot() + '/api/md', {
          'name': this.mdName
        }, function () {
          self.refresh()
        }).fail(function (error) {
          alert(error.responseText)
        })
      },

      saveMd: function () {
        if (this.selected === '') return
        console.log('saving ' + this.selected)
        const self = this
        $.ajax({
          url: constants.apiRoot() + '/api/md/' + this.selected,
          method: 'PUT',
          data: {
            'content': this.content
          }
        }).done(function () {
          self.refresh()
        }).fail(function (error) {
          alert(error.responseText)
        })
      },

      deleteMd: function () {
        if (this.selected === '') return
        console.log('deleting ' + this.selected)
        const self = this
        $.ajax({
          url: constants.apiRoot() + '/api/md/' + this.selected,
          method: 'DELETE'
        }).done(function () {
          self.selected = ''
          self.content = ''
          self.refresh()
        }).fail(function (error) {
          alert(error.responseText)
        })
      }
    },

    computed: {
      getMd: function () {
        return marked(this.content)
      }
    },

    mounted: function () {
      this.refresh()
    }
  }
</script>

<style scoped>
  * {
    text-align: center;
  }

  #controls {
    vertical-align: top;
    display: inline-block;
    width: 250px;
    margin: 0 20px 20px 20px;
  }

  #add {
    border: 0;
  }

  .list-item {
    border: solid darkslateblue 1px;
    margin: 5px;
    padding: 3px;
    height: 20px;
  }

  #editor_component {
    display: inline-block;
    vertical-align: center;
    text-align: left;
  }

  #editor {
    vertical-align: top;
    display: inline-block;
    margin: 0 20px 20px 20px;
    text-align: left;
  }

  #area {
    text-align: left;
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
    padding: 5px;
    text-align: left;
  }
</style>
