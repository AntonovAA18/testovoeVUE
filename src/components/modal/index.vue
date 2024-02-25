<template>
  <div class="modal-overlay">
    <div class="modal-window">
      <div class="modal-title">
        <span >{{title}}</span>
        <button
            class="close-button"
            @click="$emit('closeModal')"
        >
          <img src="./close.svg" alt="">
        </button>
      </div>
      <span class="modal-text">{{text}}</span>
      <slot />
    </div>
  </div>
</template>

<script>
export default {
  name: "ModalWindow",

  emits: ['closeModal'],

  props: {
    title: {
      type: String,
      default: ""
    },
    text: {
      type: String,
      default: ""
    }
  },
}
</script>

<style scoped>
  .close-button {
    position: absolute;
    right: 22px;
    top: 22px;
  }
  .modal-overlay {
    z-index: 2;
    position: absolute;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba(0, 0, 0, .5);
  }
  .modal-window {
    max-width: 500px;
    border-radius: 8px;
    background-color: #FFFFFF;
    position: relative;
    z-index: 5;
    padding: 40px 24px 40px 24px;
  }
  .modal-title {
    font-size: 24px;
    font-weight: 700;
    color: black;
    display: flex;
    justify-content: space-between;
  }
  .modal-text {
    padding: 24px 0px 24px 0px;
    display: flex;
    justify-content: start;
    text-align: start;
  }
</style>