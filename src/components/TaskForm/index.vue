<template>
    <div class="task-content-form" v-show="!edit">
      <span>{{text}}</span>
      <button
          v-contextmenu:contextmenu="{ trigger: ['contextmenu', 'click']}"
      >
        <img
            src="./dots.svg" alt=""
        >
      </button>
      <v-contextmenu ref="contextmenu">
        <v-contextmenu-item
            class="contextmenu-item"
            @click="handleEdit(text)"
        >
          <img src="./editIcon.svg" alt="">
          <span class="icon-margin">Редактировать</span>
        </v-contextmenu-item>
        <v-contextmenu-item
            class="contextmenu-item"
            @click="handleDelete(id, columnId, text)"
        >
          <img src="./deleteIcon.svg" alt="">
          <span class="icon-margin">Удалить</span>
        </v-contextmenu-item>
      </v-contextmenu>
    </div>
    <div ref="edit-area" class="edit-area" v-show="edit">
      <textarea
          id="textarea"
          class="edit-input"
          rows="5"
          ref="textarea"
          v-model="editText"
          @focus="resize"
          @keyup="resize"
      />
      <div class="edit-buttons">
        <button
            @click="$emit('editItem', id, columnId, editText)"
        >
          <img src="./Ok.svg" alt="">
        </button>
        <button
            @click="editReject(id, columnId, text)"
        >
          <img src="./reject.svg" alt="">
        </button>
      </div>
    </div>
</template>

<script>
import { directive, Contextmenu, ContextmenuItem } from "v-contextmenu";
import "v-contextmenu/dist/themes/default.css";



export default {
  directives: {
    contextmenu: directive,
  },

  components: {
    [Contextmenu.name]: Contextmenu,
    [ContextmenuItem.name]: ContextmenuItem,
  },

  name: "TaskForm",

  props: {
    id: [Number, String],
    columnId: [Number, String],
    text: {
      type: String,
      default: ""
    }
  },

  mounted() {
    this.text.length === 0 && this.handleEdit("")
  },

  emits: ['deleteItem', 'editItem', 'disableDragging'],

  data: () => ({
      edit: true,
      editText: "",
      textareaHeight: ""
  }),
  beforeMount() {
    this.edit = false

  },

  methods: {
    handleEdit () {
      this.edit = true
      this.editText = this.$props.text
      this.$emit('disableDragging', true)
    },
    handleDelete (id, columnId, text) {
      this.$emit('deleteItem', id, columnId, text)
    },
    editReject (id, columnId, text) {
     this.edit = false
     this.$emit('disableDragging', false)
      if (!this.text.length && !this.editText.length) {
        this.handleDelete(id, columnId, text)
      }
    },
    resize() {
        let element = this.$refs["textarea"];
        element.style.height = element.scrollHeight - 4 + 'px';
        document.getElementById("textarea").focus();
    }
  }
}
</script>

<style scoped>
  button {
    display: flex;
    cursor: pointer;
  }
  textarea:focus {
    outline: none;
    color: inherit;
  }
  .edit-input {
    color: inherit;
    border-radius: 8px;
    list-style: none;
    font-family: inherit;
    font-size: 14px;
    resize: none;
    width: 119px;
    overflow: hidden;
    box-sizing: border-box;
    outline: none;
    border: none;
  }
  .contextmenu-item {
    margin: 0px 8px 0px 8px;
    color: black;
    display: flex;
    align-items: center;
    justify-content: flex-start;
    font-family: "TT Interphases Pro";
  }
  .contextmenu-item:hover {
    background-color: #E1F1FF;
  }
  .edit-area {
    display: flex;
    margin: 8px 8px 0px 8px;
    padding: 8px;
    border: 1px #3D86F4 solid;
    border-radius: 8px;
    background-color: white;
  }
  .edit-buttons {
    display: flex;
    flex-direction: column;
  }
  li {
    list-style: none;
  }
  .task-content-form {
    display: flex;
    max-width: 145px;
    width: 100%;
    width: -moz-available;
    width: -webkit-fill-available;
    width: fill-available;
    align-items: flex-start;
    justify-content: space-between;
    text-align: left;
    background-color: #FFFFFF;
    padding: 8px;
    margin: 8px 8px 0px 8px;
    border-radius: 8px;
    border: 1px solid #E3E5E8;
  }
  .icon-margin {
    margin-left: 10.5px;
  }
</style>