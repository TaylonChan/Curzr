<template>
  <code>
    {{ 
      language === 'html' 
        ? html 
        : language === 'javascript' 
          ? javascript 
          : vue 
    }}
  </code>
</template>

<script>
  /* eslint-disable no-useless-escape */
  export default {
    name: 'CodeGlitchEffect',
    components: {
    },
    props: {
      language: {
        type: String,
        required: true,
        validator: function (value) {
          return [
            'html',
            'javascript',
            'vue'
          ].indexOf(value) !== -1
        }
      },
      cursorsConfig: {
        type: Object,
        required: true
      }
    },
    computed: {
      html() {
        return `
<div class="curzr" hidden></div>`
      },
      javascript() {
        return `
class GlitchEffect {
  constructor() {
    this.root = document.body
    this.cursor = document.querySelector(".curzr")

    this.distanceX = 0, 
    this.distanceY = 0,
    this.pointerX = 0,
    this.pointerY = 0,
    this.previousPointerX = 0
    this.previousPointerY = 0
    this.cursorSize = ${25 + (this.cursorsConfig.size / 5)}
    this.glitchColorB = '#00feff'
    this.glitchColorR = '#ff4f71'

    this.cursorStyle = {
      boxSizing: 'border-box',
      position: 'fixed',
      top: \`\${ this.cursorSize / -2 }px\`,
      left: \`\${ this.cursorSize / -2 }px\`,
      zIndex: '2147483647',
      width: \`\${ this.cursorSize }px\`,
      height: \`\${ this.cursorSize }px\`,
      backgroundColor: '#222',
      borderRadius: '50%',
      boxShadow: \`0 0 0 \${this.glitchColorB}, 0 0 0 \${this.glitchColorR}\`,
      transition: '100ms, transform ${this.cursorsConfig.delay}ms',
      userSelect: 'none',
      pointerEvents: 'none'
    }

    if (CSS.supports("backdrop-filter", "invert(1)")) {
      this.cursorStyle.backdropFilter = 'invert(${this.cursorsConfig.filterInvert})'
      this.cursorStyle.backgroundColor = '#fff0'
    } else {
      this.cursorStyle.backgroundColor = '#222'
    }

    this.init(this.cursor, this.cursorStyle)
  }

  init(el, style) {
    Object.assign(el.style, style)
    this.cursor.removeAttribute("hidden")
    ${
      !this.cursorsConfig.origin 
        ? 
    `
    document.body.style.cursor = 'none'
    document.body.querySelectorAll("button, label, input, textarea, select, a").forEach((el) => {
      el.style.cursor = 'inherit'
    })` 
        : 
    ``
    }
  }

  move(event) {
    this.previousPointerX = this.pointerX
    this.previousPointerY = this.pointerY
    this.pointerX = event.pageX + this.root.getBoundingClientRect().x
    this.pointerY = event.pageY + this.root.getBoundingClientRect().y
    this.distanceX = Math.min(Math.max(this.previousPointerX - this.pointerX, -10), 10)
    this.distanceY = Math.min(Math.max(this.previousPointerY - this.pointerY, -10), 10)

    if (event.target.localName === 'button' || 
        event.target.localName === 'a' || 
        event.target.onclick !== null ||
        event.target.className.includes('curzr-hover')) {
      this.hover()
    } else {
      this.hoverout()
    }

    this.cursor.style.transform = \`translate3d(\${this.pointerX}px, \${this.pointerY}px, 0)\`
    this.cursor.style.boxShadow = \`
      \${+this.distanceX}px \${+this.distanceY}px 0 \${this.glitchColorB}, 
      \${-this.distanceX}px \${-this.distanceY}px 0 \${this.glitchColorR}\`
    this.stop()
  }

  hover() {
  }

  hoverout() {
  }

  click() {
    this.cursor.style.transform += \` scale(0.75)\`
    setTimeout(() => {
      this.cursor.style.transform = this.cursor.style.transform.replace(\` scale(0.75)\`, '')
    }, 35)
  }

  stop() {
    if (!this.moving) {
      this.moving = true
      setTimeout(() => {
        this.cursor.style.boxShadow = ''
        this.moving = false
      }, 50)
    }
  }

  remove() {
    this.cursor.remove()
  }
}

(() => {
  const cursor = new GlitchEffect()
  if(!/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
    document.onmousemove = function (event) {
      cursor.move(event)
    }
    document.onclick = function () {
      cursor.click()
    }
  } else {
    cursor.remove()
  }
})()`
      },
      vue() {
        return `
<template>
  <div class="curzr" ref="curzr"></div>
</template>

<script>
  export default {
    name: 'GlitchEffect',
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
      cursorStyle() {
        return this.$refs.curzr.style
      }
    },
    mounted() {
      if(!/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
        this.cursorSize = Number(getComputedStyle(this.$refs.curzr).getPropertyValue('--size').slice(0, -2))
        this.$refs.curzr.removeAttribute("hidden")
        document.body.addEventListener('mousemove', (event) => {
          this.move(event, document.body)
        })
        document.body.addEventListener('click', () => {
          this.click()
        })
        ${
          !this.cursorsConfig.origin 
            ? 
        `
        document.body.style.cursor = 'none'
        document.body.querySelectorAll("button, label, input, textarea, select, a").forEach((el) => {
          el.style.cursor = 'inherit'
        })` 
            : 
        ``
        }
      } else {
        this.$destroy()
        this.$el.parentNode.removeChild(this.$el)
      }
    },
    methods: {
      move(event, root) {
        this.previousPointerX = this.pointerX
        this.previousPointerY = this.pointerY
        this.pointerX = event.pageX + root.getBoundingClientRect().x
        this.pointerY = event.pageY + root.getBoundingClientRect().y
        this.distanceX = Math.min(Math.max(this.previousPointerX - this.pointerX, -10), 10)
        this.distanceY = Math.min(Math.max(this.previousPointerY - this.pointerY, -10), 10)

        if (event.target.localName === 'button' || 
            event.target.localName === 'a' || 
            event.target.onclick !== null ||
            event.target.className.includes('curzr-hover')) {
          this.hover()
        } else {
          this.hoverout()
        }

        this.cursorStyle.transform = \`translate3d(\${this.pointerX}px, \${this.pointerY}px, 0)\`
        this.cursorStyle.boxShadow = \`
          \${+this.distanceX}px \${+this.distanceY}px 0 \${this.glitchColorB}, 
          \${-this.distanceX}px \${-this.distanceY}px 0 \${this.glitchColorR}\`
        
        this.stop()
      },
      hover() {
      },
      hoverout() {
      },
      click() {
        this.cursorStyle.transform += \` scale(0.75)\`
        setTimeout(() => {
          this.cursorStyle.transform = this.cursorStyle.transform.replace(\` scale(0.75)\`, '')
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
      }
    }
  }
<\/script>

<style scoped>
.curzr {
  --size:  ${25 + (this.cursorsConfig.size / 5)}px;
  --delay: ${this.cursorsConfig.delay}ms;
  --filter-invert: invert(${this.cursorsConfig.filterInvert});

  box-sizing: border-box;
  position: fixed;
  top: calc(var(--size) / -2);
  left: calc(var(--size) / -2);
  z-index: 2147483647;
  width: var(--size);
  height: var(--size);
  background-color: #222;
  border-radius: 50%;
  transition: 100ms, transform var(--delay);
  user-select: none;
  pointer-events: none;
}

@supports (backdrop-filter: invert(1)) {
  .curzr {
    background-color: #fff0;
    backdrop-filter: var(--filter-invert);
  }
}

@supports not (backdrop-filter: invert(1)) {
  .curzr {
    background-color: #222;
  }
}
</style>`
      }
    }
  }
</script>

<style lang="scss" scoped>
@import '../../style/main.scss';

code {
  font-family: $--fonts-style-y;
}
</style>