<script>
export default {
  data() {
    return {
      sheetHeight: 0, // in vh
      isSheetShown: false,
      dragPosition: undefined,
      sheetContents: undefined,
    }
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
    },
    onDragStart(event) {
      this.dragPosition = this.touchPosition(event).pageY
      this.sheetContents.classList.add('not-selectable')
      document.body.style.cursor = 'grabbing'
      document.body.style.overflow = 'hidden'
    },
    onDragMove(event) {
      console.log('ddddd')
      //   if (this.dragPosition === undefined) return

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

      if (this.sheetHeight < 25) {
        this.setIsSheetShown(false)
      } else if (this.sheetHeight > 75) {
        this.setSheetHeight(100)
      } else {
        this.setSheetHeight(50)
      }
    },
  },
}
</script>

<template>
  <div>
    <button id="open-sheet" type="button" aria-controls="sheet" @click="setIsSheetShown(true)">Open Sheet</button>

    <div id="sheet" class="column items-center justify-end" :aria-hidden="!isSheetShown">
      <div class="overlay" @click="setIsSheetShown(false)"></div>

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
  transition: opacity 0.3s, visibility 0.3s;
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
  background: #888;
  opacity: 0.5;
}

#sheet .contents {
  border-radius: 1rem 1rem 0 0;

  background: #fff;

  position: relative;
  overflow-y: hidden;

  transition: transform 0.3s, border-radius 0.3s;
  transform: translateY(0);

  max-height: 100vh;
  height: 30vh;

  max-width: 70rem;

  box-sizing: border-box;
  padding: 1rem;
  padding-top: 3rem;
}

#sheet .contents:not(.not-selectable) {
  transition: transform 0.3s, border-radius 0.3s, height 0.3s;
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
  padding: 1rem;
  cursor: grab;
}

#sheet .draggable-thumb {
  width: inherit;
  height: 0.25rem;
  background: #dcdcdc;
  border-radius: 0.125rem;
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

.row {
  display: flex;
  flex-direction: row;
}
.row.reversed-order {
  flex-direction: row-reverse;
}

.column {
  display: flex;
  flex-direction: column;
}
.column.reversed-order {
  flex-direction: column-reverse;
}

.row.items-start,
.column.items-start {
  align-items: flex-start;
}
.row.justify-start,
.column.justify-start {
  justify-content: flex-start;
}
.row.content-start,
.column.content-start {
  align-content: flex-start;
}
.row.items-center,
.column.items-center {
  align-items: center;
}
.row.justify-center,
.column.justify-center {
  justify-content: center;
}
.row.content-center,
.column.content-center {
  align-content: center;
}
.row.items-end,
.column.items-end {
  align-items: flex-end;
}
.row.justify-end,
.column.justify-end {
  justify-content: flex-end;
}
.row.content-end,
.column.content-end {
  align-content: flex-end;
}
.row.items-stretch,
.column.items-stretch {
  align-items: stretch;
}
.row.justify-stretch,
.column.justify-stretch {
  justify-content: stretch;
}
.row.content-stretch,
.column.content-stretch {
  align-content: stretch;
}
.row.items-baseline,
.column.items-baseline {
  align-items: baseline;
}
.row.justify-baseline,
.column.justify-baseline {
  justify-content: baseline;
}
.row.content-baseline,
.column.content-baseline {
  align-content: baseline;
}
.row .wrap,
.column .wrap {
  flex-wrap: wrap;
}
.row .reversed-wrap,
.column .reversed-wrap {
  flex-wrap: wrap-reverse;
}
.row .no-wrap,
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
