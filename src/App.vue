<template>
  <div id="app">
    <v-jstree :data="data"
              :item-events="itemEvents"
              :show-checkbox="false"
              multiple
              allow-batch
              whole-row
              draggable
              :contextmenu="contextmenu"
              @item-click="itemClick"
              @item-drag-start="itemDragStart"
              @item-drag-end="itemDragEnd"
              @item-drop-before = "itemDropBefore"
              @item-drop="itemDrop"
              ref="tree"></v-jstree>
    <v-contextmenu ref="contextmenu">
      <v-contextmenu-item @click="clickmenu(1)">菜单1</v-contextmenu-item>
      <v-contextmenu-item @click="clickmenu(2)">菜单2</v-contextmenu-item>
      <v-contextmenu-item @click="clickmenu(3)">菜单3</v-contextmenu-item>
    </v-contextmenu>
  </div>
</template>

<script>
import VJstree from  './components/tree'

export default {
  name: 'app',
  data(){
    return {
      searchText: '',
      itemEvents: {
        // mouseover: function () {
        //   console.log('mouseover')
        // },
        contextmenu: function () {
          arguments[2].preventDefault()
          arguments[0].contextmenu(arguments[1],arguments[2])
        }
      },
      data: [
        {
          "text": "Same but with checkboxes",
          "children": [
            {
              "text": "initially selected",
              "selected": true
            },
            {
              "text": "custom icon",
              "icon": "fa fa-warning icon-state-danger"
            },
            {
              "text": "initially open",
              "icon": "fa fa-folder icon-state-default",
              "opened": true,
              "children": [
                {
                  "text": "Another node"
                }
              ]
            },
            {
              "text": "custom icon",
              "icon": "fa fa-warning icon-state-warning"
            },
            {
              "text": "disabled node",
              "icon": "fa fa-check icon-state-success",
              "disabled": true
            }
          ]
        },
        {
          "text": "Same but with checkboxes",
          "opened": true,
          "children": [
            {
              "text": "initially selected",
              "selected": true
            },
            {
              "text": "custom icon",
              "icon": "fa fa-warning icon-state-danger"
            },
            {
              "text": "initially open",
              "icon": "fa fa-folder icon-state-default",
              "opened": true,
              "children": [
                {
                  "text": "Another node"
                }
              ]
            },
            {
              "text": "custom icon",
              "icon": "fa fa-warning icon-state-warning"
            },
            {
              "text": "disabled node",
              "icon": "fa fa-check icon-state-success",
              "disabled": true
            }
          ]
        },
        {
          "text": "And wholerow selection"
        },
        {
          "text": "drag disabled",
          "icon": "fa fa-warning icon-state-danger",
          "dragDisabled": true
        },
        {
          "text": "drop disabled",
          "icon": "fa fa-warning icon-state-danger",
          "dropDisabled": true
        }
      ],
    }
  },
  methods:{
    contextSelected(title){
      console.log(222222222222,title)
      this.$emit('contextSelected', title)
    },
    clickmenu(data){
      console.log(data)
    },
    contextmenu(data,mouseevent){
      // this.showContext = true;
      // this.MouseEvent = data
      // console.log(this.showContext,this.MouseEvent)
      // console.log(33333333333,data)
      console.log(data)
      const postition = {
        top: mouseevent.clientY,
        left: mouseevent.clientX,
      }
      this.$refs.contextmenu.show(postition)
    },
    itemClick (node) {
      this.editingNode = node
      this.editingItem = node.model
      console.log(node.model.text + ' clicked !')
    },
    itemDragStart (node) {
      console.log(node.model.text + ' drag start !')
    },
    itemDragEnd (node) {
      console.log(node.model.text + ' drag end !')
    },
    itemDropBefore (node, item, draggedItem , e) {
      if (!draggedItem) {
        item.addChild({
          text: "newNode",
          value: "newNode"
        })
      }
    },
    itemDrop (node, item) {
      var sortBy = function(attr,rev) {
        if (rev == undefined) {
          rev = 1;
        } else {
          rev = (rev) ? 1 : -1;
        }
        return function (a, b) {
          a = a[attr];
          b = b[attr];
          if (a < b) {
            return rev * -1;
          }
          if (a > b) {
            return rev * 1;
          }
          return 0;
        }
      }
      item.children.sort(sortBy('text', true))
      console.log(node.model.text + ' drop !')
    },
    inputKeyUp: function () {
      var text = this.searchText
      const patt = new RegExp(text);
      this.$refs.tree.handleRecursionNodeChilds(this.$refs.tree, function (node) {
        if (text !== '' && node.model !== undefined) {
          const str = node.model.text
          if (patt.test(str)) {
            node.$el.querySelector('.tree-anchor').style.color = 'red'
          } else {
            node.$el.querySelector('.tree-anchor').style.color = '#000'
          } // or other operations
        } else {
          node.$el.querySelector('.tree-anchor').style.color = '#000'
        }
      })
    },
    addChildNode: function () {
      if (this.editingItem.id !== undefined) {
        this.editingItem.addChild({
          text: "newNode"
        })
      }
    },
    removeNode: function () {
      if (this.editingItem.id !== undefined) {
        var index = this.editingNode.parentItem.indexOf(this.editingItem)
        this.editingNode.parentItem.splice(index, 1)
      }
    },
    addBeforeNode: function () {
      if (this.editingItem.id !== undefined) {
        this.editingItem.addBefore({
          text: this.editingItem.text + " before"
        }, this.editingNode)
      }
    },
    addAfterNode: function () {
      if (this.editingItem.id !== undefined) {
        this.editingItem.addAfter({
          text: this.editingItem.text + " after"
        }, this.editingNode)
      }
    },
    openChildren: function () {
      if (this.editingItem.id !== undefined) {
        this.editingItem.openChildren()
      }
    },
    closeChildren: function () {
      if (this.editingItem.id !== undefined) {
        this.editingItem.closeChildren()
      }
    },
    refreshNode: function () {
      this.asyncData = [
        this.$refs.tree2.initializeLoading()
      ]
      this.$refs.tree2.handleAsyncLoad(this.asyncData, this.$refs.tree2)
    },
    customItemClick: function (node ,item, e) {
      e.stopPropagation()
      var index = node.parentItem.indexOf(item)
      node.parentItem.splice(index, 1)
    },
    customItemClickWithCtrl: function () {
      console.log('click + ctrl')
    },
    fillData: function () {
      if (this.editingItem.id !== undefined) {
        for (var i = 0; i < this.filesToAdd; i++) {
          this.filesToAddIndex++
          this.editingItem.addChild({
            text: "File" + this.filesToAddIndex,
            icon: "fa fa-file icon-state-danger"
          })
        }
      }
    }
  },
  components: {
    VJstree
  }
}
</script>
