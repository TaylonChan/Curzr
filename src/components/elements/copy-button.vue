<template>
  <button id="copy-button" class="copy-button" @click="copyText()">
    <img v-if="!isCopied" src="../../assets/icon/copy-icon.svg" alt="copy icon" width="16" height="16">
    <img v-else src="../../assets/icon/complete-icon.svg" alt="complete icon" width="16" height="16">
    <span v-if="!isCopied">Copy to clipboard</span>
    <span v-else role="alert">Copied</span>
  </button>
</template>

<script>
  export default {
    name: 'CopyButton',
    components: {
    },
    props: {
      copiedText: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        isCopied: false,
        delay: 3000
      }
    },
    methods: {
      /**
       * Return Promise
       * 
       * @param {string} copiedText
       */
      copyToClipboard(copiedText) {
        // Navigator Clipboard API Needs Secure Context (HTTPS)
        if (navigator.clipboard && window.isSecureContext) {
          // Navigator Clipboard API Method
          return navigator.clipboard.writeText(copiedText)
        } else {
          // Text Area Method
          let textArea = document.createElement("textarea")
          textArea.value = copiedText
          // Make The Textarea Out Of Viewport
          textArea.style.position = "fixed"
          textArea.style.opacity = 0
          document.body.appendChild(textArea)
          textArea.select()
          return new Promise((res, rej) => {
            // Here The Magic Happens
            document.execCommand('copy') ? res() : rej()
            textArea.remove()
          });
        }
      },
      /**
       * Copy the text from the props 'copiedText'
       */
      copyText() {
        this.copyToClipboard(this.copiedText).then(() => {
        }).catch(() => {
          alert('Failed to copy to clipboard!')
          console.error('Failed to copy to clipboard!')
        })
        this.setCopyStatus()
      },
      /**
       * Set the status true after click on the copy button and reset the status in certain delay time
       */
      setCopyStatus() {
        this.isCopied = true
        setTimeout(() => {
          this.isCopied = false
        }, this.delay)
      }
    }
  }
</script>

<style lang="scss" scoped>
@import '../../style/main.scss';

.copy-button {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  bottom: 1rem;
  left: 50%;
  transform: translateX(-50%);
  width: calc(100% - 2rem);
  height: 50px;
  border: 2px solid #fff2;
  border-radius: $--common-radius / 2;
  box-shadow: 0 16px 40px #0004;
  transition: 250ms;

  img {
    width: 16px;
    height: auto;
    margin-right: 1rem;
    margin-top: 2px;
  }

  span {
    color: #fff0;
    background: linear-gradient(90deg, #fff 0%, #fffa 100%);
    background-clip: text;
    text-fill-color: transparent;
  }
}

@supports (backdrop-filter: blur(5px)) {
  .copy-button {
    background-color: #fff0;
    backdrop-filter: blur(5px);

    &:hover {
      background-color: #fff1;
    }
  }
}

@supports not (backdrop-filter: blur(5px)) {
  .copy-button {
    background-color: #06252cee;

    &:hover {
      background-color: #06252caa;
    }
  }
}
</style>