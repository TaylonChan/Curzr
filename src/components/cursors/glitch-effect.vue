<template>
  <div class="curzr-glitch-effect" ref="cursor"></div>
</template>

<script>
  export default {
    name: 'GlitchEffect',
    props: {
      cursorsConfig: {
        type: Object
      }
    },
    data() {
      return {
        distanceX: 0, 
        distanceY: 0,
        pointerX: 0,
        pointerY: 0,
        previousPointerX: 0,
        previousPointerY: 0,
        moving: false,
        glitchColorB: '#00feff',
        glitchColorR: '#ff4f71'
      }
    },
    computed: {
      /**
       * The cursor size from the CSS variable
       */
      cursorSize() {
        return Number(getComputedStyle(this.$refs.cursor).getPropertyValue('--size').slice(0, -2))
      },
      cursorStyle() {
        return this.$refs.cursor.style
      }
    },
    mounted() {
      /**
       * The cursor status of the default cursor visibility
       */
      if (!this.cursorsConfig.origin) {
        this.setOriginalCursor('none')
      }
    },
    watch: {
      /**
       * Change the value of cursor after cursorsConfig changed from model edit or adjustment bar
       * 
       * @param {object} configValue
       */
      cursorsConfig: {
        handler(configValue) {
          this.cursorStyle.setProperty('--size', (this.cursorSize + (configValue.size / 2)) + 'px')
          this.cursorStyle.setProperty('--delay', configValue.delay + 'ms')
          if (this.cursorsConfig.from === 'model') {
            this.cursorStyle.setProperty('--filter-invert', `invert(${configValue.filterInvert})`)
          }
          !this.cursorsConfig.origin ? this.setOriginalCursor('none') : this.setOriginalCursor('')
        },
        deep: true
      }
    },
    methods: {
      /**
       * Center the position of cursor after its container loaded 
       */
      init() {
        this.cursorStyle.top = (getComputedStyle(this.$refs.cursor).getPropertyValue('--size').slice(0, -2) / -2) + 'px'
        this.cursorStyle.left = (getComputedStyle(this.$refs.cursor).getPropertyValue('--size').slice(0, -2) / -2) + 'px'
        this.cursorStyle.transition = ''
        this.$refs.cursor.addEventListener('click', this.click)
      },
      /**
       * Get the cursor position by event and apply them to the transform property of the cursor 
       * 
       * @param {object} event
       * @param {object} cursorBlock
       */
      move(event, cursorBlock) {
        this.previousPointerX = this.pointerX
        this.previousPointerY = this.pointerY
        this.pointerX = event.pageX - cursorBlock.getBoundingClientRect().x
        this.pointerY = event.pageY - cursorBlock.getBoundingClientRect().y + this.$root.$el.getBoundingClientRect().y
        this.distanceX = Math.min(Math.max(this.previousPointerX - this.pointerX, -10), 10)
        this.distanceY = Math.min(Math.max(this.previousPointerY - this.pointerY, -10), 10)

        event.target.localName === 'button' || event.target.localName === 'a' || event.target.parentElement.localName === 'button' 
          ? this.hover() 
          : this.hoverout()

        this.cursorStyle.transform = `translate3d(${this.pointerX}px, ${this.pointerY}px, 0)`
        this.cursorStyle.boxShadow = `
          ${+this.distanceX}px ${+this.distanceY}px 0 ${this.glitchColorB}, 
          ${-this.distanceX}px ${-this.distanceY}px 0 ${this.glitchColorR}`
        
        this.stop()
      },
      /**
       * Apply the transform property when triggered by the 'mousemove' event listener
       */
      hover() {
      },
      /**
       * Apply the transform property when triggered by the 'mouseleave' event listener
       */
      hoverout() {
      },
      /**
       * Apply the transform property when triggered by the 'click' event listener
       */
      click() {
        this.cursorStyle.transform += ` scale(0.75)`
        setTimeout(() => {
          this.cursorStyle.transform = this.cursorStyle.transform.replace(` scale(0.75)`, '')
        }, 35)
      },
      stop() {
        if (!this.moving) {
          this.moving = true
          setTimeout(() => {
            this.cursorStyle.boxShadow = ''
            this.moving = false
          }, 50)
        }
      },
      setOriginalCursor(value) {
        this.$refs.cursor.parentElement.style.cursor = value
        this.$refs.cursor.parentElement.querySelectorAll("button, label, input, textarea, select, a").forEach((el) => {
          el.style.cursor = 'inherit'
        })
      },
      /**
       * Center the position of cursor when leaving its container
       */
      reset() {
        this.cursorStyle.top = ''
        this.cursorStyle.left = ''
        this.cursorStyle.boxShadow = ''
        this.cursorStyle.transform = ''
        this.cursorStyle.transition = '500ms'
        this.$refs.cursor.removeEventListener('click', this.click)
      }
    }
  }
</script>

<style lang="scss" scoped>
.curzr-glitch-effect {
  --size:  25px;
  --delay: 100ms;
  --filter-invert: invert(1);

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 1;
  width: var(--size);
  height: var(--size);
  background-color: #222;
  border-radius: 50%;
  transition: 100ms, transform var(--delay);
  user-select: none;
  pointer-events: none;
}

@supports (backdrop-filter: invert(1)) {
  .curzr-glitch-effect {
    background-color: #fff0;
    backdrop-filter: var(--filter-invert);
  }
}

@supports not (backdrop-filter: invert(1)) {
  .curzr-glitch-effect {
    background-color: #222;
  }
}
</style>