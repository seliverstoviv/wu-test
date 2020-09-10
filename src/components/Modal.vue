<template>
  <transition name="fade" appear>
    <div
      class="modal-overlay"
      v-if="visible"
      @click="clickOverlay"
    >
      <transition name="slide">
        <div class="modal" :style="`width: ${width}px`" v-if="visible">
          <div class="modal__header">
            <h5 class="modal__title">{{ title }}</h5>
            <button
              type="button"
              class="btn btn__close"
              @click="closeModal()">
              <span aria-hidden="true">×</span>
            </button>
          </div>
          <div class="modal__body">
            <slot></slot>
          </div>
        </div>
      </transition>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: 'noName',
    },
    width: {
      type: Number,
      default: 400,
    },
    visible: {
      type: Boolean,
      default: true,
    },
  },
  methods: {
    closeModal() {
      this.$emit('set-visible', !this.visible);
    },
    clickOverlay(e) {
      if (e.target.className === 'modal-overlay') {
        this.closeModal();
      }
    },
  },
  mounted() {
    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape' && this.visible) this.closeModal();
    });
  },
};
</script>
