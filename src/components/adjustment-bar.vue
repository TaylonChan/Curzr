<template>
  <section id="adjustment-bar" class="adjustment-bar">
    <div class="adjustment-container">
      <div class="size-adjustment">
        <img src="../assets/icon/Scale.svg" alt="size adjustment icon" width="20">
        <small>Resize</small>
        <range-bar 
          id="range-size"
          class="range-bar"
          :range-value="cursorsConfig.size"
          :minmax="[-25, 25]"
          @changeRangeValue="changeRangeValue"
        >
        </range-bar>
      </div>
      <div class="transition-adjustment">
        <img src="../assets/icon/Pathfinder.svg" alt="delay adjustment icon" width="20">
        <small>Delay</small>
        <range-bar 
          id="range-delay"
          class="range-bar"
          :range-value="cursorsConfig.delay"
          :minmax="[0, 200]"
          @changeRangeValue="changeRangeValue"
        >
        </range-bar>
      </div>
      <div class="grid-view-adjustment">
        <div class="wrapper wrapper-active" @click="changeState('cursor-content-grid')" ref="gridBtn">
          <img src="../assets/icon/Grid.svg" alt="size adjustment icon" width="20">
        </div>
        <div class="wrapper" @click="changeState('cursor-content-list')" ref="listBtn">
          <img src="../assets/icon/List.svg" alt="size adjustment icon" width="20">
        </div>
      </div>
    </div>
  </section>
</template>

<script>
  import RangeBar from '@/components/elements/range-bar.vue'

  export default {
    name: 'AdjustmentBar',
    components: {
      'range-bar': RangeBar
    },
    props: {
      cursorsConfig: {
        type: Object,
        required: true
      }
    },
    methods: {
      /**
       * Emit the value to the parent
       * 
       * @param {object} event
       * @event changeRangeValue
       */
      changeRangeValue(event) {
        this.$emit('changeRangeValue', event)
      },
      /**
       * Apply and remove the class to the certain button according to the @param {string} mode
       * 
       * @param {string} mode
       * @event click
       */
      changeState(mode) {
        if (mode === 'cursor-content-grid') {
          this.$refs.gridBtn.classList.add('wrapper-active')
          this.$refs.listBtn.classList.remove('wrapper-active')
        } else {
          this.$refs.gridBtn.classList.remove('wrapper-active')
          this.$refs.listBtn.classList.add('wrapper-active')
        }
        this.$store.commit({
          type: 'changeMode',
          viewMode: mode
        })
      }
    }
  }
</script>

<style lang="scss">
@import '../style/main.scss';
.adjustment-bar {
  display: flex;
  background-color: #fff;
  border-bottom: 1px solid $--section-line-color;

  .adjustment-container {
    display: flex;
    flex-wrap: wrap;

    & > * {
      display: flex;
      align-items: center;
      padding: 1rem 1.5rem;
      height: $--adjustbar-height;

      img {
        width: 20px;
        opacity: .2;
      }

      small {
        width: 2.875rem;
        opacity: .4;
        margin-left: 1rem;
      }

      .range-bar {
        width: 120px;
      }
    }

    .grid-view-adjustment {

      .wrapper {
        @include flex-center;
        padding: .5rem;
        border-radius: 6px;
        transition: 250ms;
        cursor: pointer;

        &:hover {
          box-shadow: 0 0 0 2px $--section-line-color inset;
        }

        &:not(:first-child) {
          margin-left: .25rem;
        }

        img {
          transform: rotate(90deg);
        }
      }

      .wrapper-active {
        background-color: $--section-line-color;
      }
    }
  }
}
</style>