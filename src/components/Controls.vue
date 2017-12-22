<template>
  <div id="controls">
    <div id="add" class="list-item">
      <input placeholder="Name" v-model="mdName">
      <button v-on:click="createMd">Add</button>
    </div>
    <div id="mdList">
      <div class="list-item" v-for="md in mds" v-on:click="mdSelect(md)">
        <div v-if="md._id === selected" style="background-color: aqua">{{md._id}}</div>
        <div v-else>{{md._id}}</div>
      </div>
    </div>
  </div>
</template>

<script>
  import $ from 'jquery'
  import editor from '@/components/Editor.vue'

  export default {
    data: function () {
      return {
        mdName: '',
        selected: '',
        mds: []
      }
    },

    methods: {
      createMd: function () {
        this.mdName = this.mdName.trim()
        if (this.mdName === '') return
        $.post('/api/md', {
          'name': this.mdName
        }, function () {
          this.refresh()
        }).fail(function (error) {
          alert(error.responseText)
        })
      },

      mdSelect: function (md) {
        this.selected = md._id
        editor.content = md.content
      },

      refresh: function () {
        this.mdName = ''
        $.get('/api/md', function (response, status) {
          this.mds = response.reverse()
        })
      }
    }
  }
</script>

<style scoped>
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
</style>
