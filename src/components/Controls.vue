<template>
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
</template>

<script>
  import $ from 'jquery'
  import constants from '@/constants'
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

      selectMd: function (md) {
        console.log('selected id=' + md._id)
        this.selected = md._id
        editor.content = md.content
      },

      refresh: function () {
        console.log('refreshing')
        this.mdName = ''
        const self = this
        $.get(constants.apiRoot() + '/api/md', function (response, status) {
          self.mds = response.reverse()
        })
      }
    },

    mounted: function () {
      this.refresh()
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
