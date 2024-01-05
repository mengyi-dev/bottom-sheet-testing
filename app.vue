<script>
export default {
  props: {
    open: { type: Boolean, default: true },
  },
  data() {
    return {
      sheetHeight: 0, // in vh
      isSheetShown: false,
      dragPosition: undefined,
      sheetContents: undefined,
    }
  },
  watch: {
    open: {
      immediate: true,
      handler(val) {
        this.setIsSheetShown(val)
      },
    },
  },
  mounted() {
    const $ = document.querySelector.bind(document)
    this.sheetContents = $('#sheet .contents')
    console.log(this.dragPosition)

    window.addEventListener('mousemove', this.onDragMove)
    window.addEventListener('touchmove', this.onDragMove)

    window.addEventListener('mouseup', this.onDragEnd)
    window.addEventListener('touchend', this.onDragEnd)
  },
  methods: {
    setSheetHeight(value) {
      this.sheetHeight = Math.max(0, Math.min(100, value))
      this.sheetContents.style.height = `${this.sheetHeight}vh`

      if (this.sheetHeight === 100) {
        this.sheetContents.classList.add('fullscreen')
      } else {
        this.sheetContents.classList.remove('fullscreen')
      }
    },
    touchPosition(event) {
      return event.touches ? event.touches[0] : event
    },
    setIsSheetShown(value) {
      this.isSheetShown = value
      //   this.$emit('close')
    },
    onDragStart(event) {
      this.dragPosition = this.touchPosition(event).pageY
      this.sheetContents.classList.add('not-selectable')
      document.body.style.cursor = 'grabbing'
      document.body.style.overflow = 'hidden'
    },
    onDragMove(event) {
      const y = this.touchPosition(event).pageY
      const deltaY = this.dragPosition - y
      const deltaHeight = (deltaY / window.innerHeight) * 100

      this.setSheetHeight(this.sheetHeight + deltaHeight)
      this.dragPosition = y
    },
    onDragEnd() {
      this.dragPosition = undefined
      this.sheetContents.classList.remove('not-selectable')
      document.body.style.cursor = ''
      document.body.style.overflow = ''

      if (this.sheetHeight < 25) {
        this.handleClose()
      } else if (this.sheetHeight > 75) {
        this.setSheetHeight(100)
      } else {
        this.setSheetHeight(50)
      }
    },
    handleClose() {
      this.$emit('close')
      this.setIsSheetShown(false)
    },
  },
}
</script>

<template>
  <div>
    <div id="sheet" class="column items-center justify-end" :aria-hidden="!isSheetShown">
      <div class="overlay" @click="handleClose"></div>

      <div class="contents column" style="background: #ffff; box-shadow: rgba(0, 0, 0, 0.16) 0px 1px 4px; width: 100%">
        <header class="controls">
          <div class="draggable-area" @mousedown="onDragStart" @touchstart="onDragStart">
            <div class="draggable-thumb"></div>
          </div>
        </header>

        <main class="body fill column">
          <slot></slot>
        </main>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Copyright (c) 2022 Ivan Teplov */
body {
  background: #fff;
  color: #000;

  overflow: hidden;
  line-height: 1.5;

  -webkit-tap-highlight-color: transparent;
}

button {
  padding: 1rem;
  font-size: 1rem;
  border-radius: 1rem;
  border: 0.0625rem solid #dcdcdc;

  background: #fff;
  color: #000;
  cursor: pointer;
}

#sheet {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 2;
  visibility: visible;
  transition: opacity 0.5s, visibility 0.5s;
}

#sheet[aria-hidden='true'] {
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
}

#sheet .overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: -1;
  background: rgba(0, 0, 0, 0.5);
  opacity: 0.5;
}

#sheet .contents {
  border-radius: 16px 16px 0 0;

  background: #fff;

  position: relative;
  overflow-y: hidden;

  transition: transform 0.5s, border-radius 0.5s;
  transform: translateY(0);

  max-height: 100vh;
  height: 30vh;

  max-width: 70rem;

  box-sizing: border-box;
  padding: 1rem;
  padding-top: 3rem;
}

#sheet .contents:not(.not-selectable) {
  transition: transform 0.5s, border-radius 0.5s, height 0.5s;
}

#sheet .contents.fullscreen {
  border-radius: 0;
}

#sheet[aria-hidden='true'] .contents {
  transform: translateY(100%);
}

#sheet .draggable-area {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  width: 3rem;
  margin: auto;
  padding: 12px;
  cursor: grab;
}

#sheet .draggable-thumb {
  width: 34px;
  height: 2px;
  background: #dcdcdc;
  border-radius: 0.125rem;
  margin: 0 auto;
}

#sheet .close-sheet {
  position: absolute;
  right: 0;
  top: 0;
  border: none;
}

#sheet .body {
  height: 100%;
  overflow-y: auto;
  gap: 1rem;
}

.column {
  display: flex;
  flex-direction: column;
}
.column.reversed-order {
  flex-direction: column-reverse;
}

.column.items-start {
  align-items: flex-start;
}

.column.justify-start {
  justify-content: flex-start;
}

.column.content-start {
  align-content: flex-start;
}

.column.items-center {
  align-items: center;
}
.column.justify-center {
  justify-content: center;
}
.column.content-center {
  align-content: center;
}
.column.items-end {
  align-items: flex-end;
}
.column.justify-end {
  justify-content: flex-end;
}
.column.content-end {
  align-content: flex-end;
}
.column.items-stretch {
  align-items: stretch;
}
.column.justify-stretch {
  justify-content: stretch;
}
.column.content-stretch {
  align-content: stretch;
}
.column.items-baseline {
  align-items: baseline;
}
.column.justify-baseline {
  justify-content: baseline;
}
.column.content-baseline {
  align-content: baseline;
}
.column .wrap {
  flex-wrap: wrap;
}
.column .reversed-wrap {
  flex-wrap: wrap-reverse;
}
.column .no-wrap {
  flex-wrap: nowrap;
}

.fill {
  flex-grow: 1;
  flex-shrink: 0;
}

.cursor-pointer {
  cursor: pointer;
}

.not-selectable {
  user-select: none;
}

.selectable {
  user-select: auto;
}
</style>
