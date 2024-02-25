<template>
  <div id="app">
    <div class="table">
      <div
          v-for="(item) in TableColumns"
          v-bind:key="item.id"
      >
          <div
              class="column-header"
              :style="{'background-color': item.headerColor}"
          >
            {{item.title}}
          </div>
        <div class="column-background column-style">
          <div
              style="height: 100%"
              @drop="onDrop($event, item.id)"
              @dragover.prevent
              @dragenter.prevent
          >
            <PerfectScrollbar>
              <div
                  v-for="a in TableData[item.id]"
                  v-bind:key="a"
                  :draggable="!draggable"
                  @dragstart="onDragStart($event, {text: a.text, id: a.id, colId: item.id})"
              >
                <TaskForm
                    v-bind:id="a.id"
                    v-bind:text="a.text"
                    v-bind:column-id="item.id"
                    @delete-item="handleOpenModal"
                    @edit-item="handleEdit"
                    @disable-dragging="dragging"
                />
              </div>
              <button
                  style="margin-top: 9px; margin-bottom: 9px"
                  @click="addItem(item.id, TableData[item.id].length)"
              >
                <img
                    src="./icons/plusIcon.svg"
                    alt=""
                    style="padding-right: 8px;"
                >
                Добавить
              </button>
            </PerfectScrollbar>
          </div>
        </div>
      </div>
    </div>
    <ModalWindow
        v-if="openModal"
        v-bind:text="modalData.text"
        title="Удалить задачу?"
        @close-modal="closeModal"
    >
      <div class="flex jc-center flex-gap-23">
        <button
            @click="handleDelete"
            class="modal-button"
        >
          Удалить
        </button>
        <button
            @click="closeModal"
            class="modal-button"
        >
          Отменить
        </button>
      </div>
    </ModalWindow>
  </div>
</template>

<script>
import { PerfectScrollbar } from 'vue3-perfect-scrollbar'
import TaskForm from "@/components/TaskForm";
import { taskTableData } from "@/configs/tableData";
import ModalWindow from "@/components/modal";

// Конфиг колонок таблицы
import {tableColumnConfig} from "@/configs/tableColumnConfig";


export default {
  name: 'App',
  components: {
    ModalWindow,
    PerfectScrollbar,
    TaskForm,
  },

  beforeMount() {
    this.TableData = taskTableData
  },
  data: () => {
    return {
      TableColumns: tableColumnConfig,
      TableData: [],
      openModal: false,
      draggable: false,
      modalData: {}
    }
  },
  methods: {
    addItem(id, length) {
      this.TableData[id].push({id: length++, text: ""})
    },
    onDragStart(evt, item) {
      evt.dataTransfer.dropEffect = 'move'
      evt.dataTransfer.effectAllowed = 'move'
      evt.dataTransfer.setData('id', item.id)
      evt.dataTransfer.setData('text', item.text)
      evt.dataTransfer.setData('colId', item.colId)
    },
    onDrop(evt, id) {
      const { TableData } = this
      const itemId = evt.dataTransfer.getData('id')
      const itemText = evt.dataTransfer.getData('text')
      const colID = evt.dataTransfer.getData('colId')
      const newData = TableData.map((item, index) => {
        if (index === id) {
          return  [...item, {id: itemId, text: itemText}]
        } return item
      })
      newData[colID].splice(itemId, 1)
      this.TableData = newData
    },
    handleEdit(id, colId, text) {
      const { TableData } = this
      this.TableData = TableData.map((item, index) => {
        if (index === colId) {
          return item.map(a => {
            return a.id === id ? {...a, text: text} : a
          })
        }
        return item
      })
      this.dragging(false)
    },
    closeModal () {
      this.openModal = false
    },
    dragging (a) {
      this.draggable = a
    },
    handleOpenModal (id, colId, text) {
      this.modalData = {id, colId, text}
      if (!text.length) {
        this.TableData = this.TableData.map((item, index) => {
          if (index === colId) {
            return item.filter(a => a.id !== id)
          }
          return item
        })
      } else this.openModal = true
    },
    handleDelete () {
      const { id, colId } = this.modalData
      const { TableData } = this
      this.openModal = false
      window.alert(`Задача "${this.modalData.text}" удалена`)
      this.modalData = {}
      this.TableData = TableData.map((item, index) => {
        if (index === colId) {
          return item.filter(a => a.id !== id)
        }
        return item
      })
    }
  },
}
</script>

<style src="vue3-perfect-scrollbar/dist/vue3-perfect-scrollbar.css" />
<style>
  #app {
    display: flex;
    justify-content: center;
    height: 100%;
    font-family: "TT Interphases Pro";
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
  .table {
    margin: 48px 52px 52px 28px;
    width: 100%;
    display: flex;
    justify-content: center;
    gap: 8px;
  }
  .column-style {;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    border-radius: 0px 0px 8px 8px;
    font-size: 14px;
    border: 1px solid #E3E5E8;
    width: 177.6px;
    height: 100%;
    overflow: hidden;
  }
  button {
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-left: 8px;
    color: #3D86F4;
  }
  .column-header {
    border-radius: 8px 8px 0 0;
    height: 32px;
    display: flex;
    font-size: 14px;
    font-weight: 585;
    width: 100%;
    justify-content: center;
    align-items: center;
  }
  .column-background {
    background-color: #F7F7F7
  }
  .modal-button {
    width: 202px;
    height: 36px;
    font-size: 14px;
    color: black;
    border: 1px #C4CAD4 solid;
    border-radius: 8px;
  }
</style>
