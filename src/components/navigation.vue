<template>
  <nav id="navigation" class="navigation" ref="navigation">
    <div class="slide-btn" @click="toggleSidemenu($event)">
      <img src="../assets/icon/btn-arrow.svg" alt="button arrow" width="100" height="100">
    </div>
    <a href="https://github.com/TaylonChan/Curzr" target="_blank" class="github-btn">
      <img src="../assets/icon/github.png" alt="github button" width="20" height="20">
      <h6 class="btn-name"><span>GITHUB</span></h6>
    </a>
    <div class="logo">
      <img src="../assets/logo/curzr_logo-long.png" alt="curzr logo" width="120" height="45">
    </div>
  </nav>
</template>

<script>
  export default {
    name: 'NavigationBar',
    components: {
      
    },
    data() {
      return {
        toggleStatus: true
      }
    },
    props: {
      
    },
    computed: {
      
    },
    methods: {
      /**
       * Toggle the sidemenu by the value of the toggleStatus
       * 
       * @param {object} event
       * @event click
       */
      toggleSidemenu(event) {
        if (this.toggleStatus) {
          if (getComputedStyle(this.$refs.navigation).getPropertyValue('--sidebar-status') == 1) {
            this.$parent.$el.childNodes[1].style.paddingLeft = '0px'
            this.$parent.$children[0].$el.style.left = -this.$parent.$children[0].$el.offsetWidth + 'px'
            this.$refs.navigation.style.width = '100%'
            event.target.style.left = '2rem'
            event.target.style.transform = 'rotate(180deg)'
          } else if (getComputedStyle(this.$refs.navigation).getPropertyValue('--sidebar-status') == 0) {
            this.$parent.$children[0].$el.style.left = '0px'
            event.target.style.transform = 'rotate(0deg)'
          }
          this.toggleStatus = false
        } else {
          this.$parent.$el.childNodes[1].style.paddingLeft = ''
          this.$parent.$children[0].$el.style.left = ''
          this.$refs.navigation.style.width = ''
          event.target.style.left = ''
          event.target.style.transform = ''
          this.toggleStatus = true
        }
      }
    }
  }
</script>

<style lang="scss">
@import '../style/main.scss';
.navigation {
  --sidebar-status: 1;

  position: fixed;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  z-index: 10;
  width: calc(100% - $--sidemenu-width);
  height: $--nav-height;
  background-color: #fff;
  box-shadow: 0 1px 0 $--section-line-color;
  transition: 500ms;

  .slide-btn {
    @include flex-center;
    position: absolute;
    left: 0;
    transform: translateX(-50%);
    width: 40px;
    height: 40px;
    background-color: #fff;
    border-radius: 50%;
    box-shadow: 0 0 0 1px $--section-line-color;
    transition: 250ms;
    cursor: pointer;
    
    &:hover {
      background-color: $--section-line-color;
    }

    img {
      position: relative;
      right: 1px;
      width: 12px;
      height: auto;
      pointer-events: none;
    }
  }

  .github-btn {
    display: flex;
    align-items: center;
    height: 100%;
    border-left: 1px solid $--section-line-color;
    padding: 1rem 2rem;
    transition: 250ms;
    background-position: center;
    background-size: cover;
    background-repeat: no-repeat;
    background-color: #fff;

    &:hover {
      background-image: url('../assets/github-background.png');

      & > img, > .btn-name {
        filter: invert(100%);
      }
    }

    img {
      width: 20px;
      height: auto;
      margin-right: 1rem;
    }

    .btn-name {
      letter-spacing: 2px;
    }
  }

  .logo {
    display: none;
  }
}

@media only screen and (max-width: 1024px) {
  .navigation {
    --sidebar-status: 0;

    width: 100%;

    .slide-btn {
      left: 2rem;
      transform: rotate(180deg);
    }

    .github-btn {

      img {
        margin-right: 0;
      }

      span {
        display: none;
      }
    }

    .logo {
      @include flex-center;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);

      img {
        width: 120px;
        height: auto;
      }
    }
  }
}
</style>