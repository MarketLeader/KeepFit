<template>
  <div class="column">
    <div class="message is-normal" :class="tileClasses">

      <!-- Header -->
      <div class="message-header">
        <p>{{ item.type }}</p>
        <p class="goal" :class="darken[colorClass]">
          <i class="far fa-crosshairs"></i>
          {{ item.target }}
        </p>
      </div>

      <!-- Body -->
      <div class="message-body">
        <div class="level">
          <div class="level-right">
            <p class="title">
              <span class="is-size-4">
                <i class="change-icon animated far" :class="change"></i>
              </span>
              {{ item.current }}
              <span class="is-size-4 has-text-weight-light has-text-grey-light" v-if="item.unit">
                {{ item.unit }}
              </span>
            </p>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'kpi-tile',
  props: {
    item: {}
  },

  data: () => ({
    threshold: {
      low: 5,
      medium: 25,
      high: 50
    },
    colorClass: 'is-dark',
    darken: {
      'is-dark': 'has-text-grey',
      'is-success': 'has-text-success-darker',
      'is-warning': 'has-text-warning-darker',
      'is-danger': 'has-text-danger-darker'
    }
  }),

  computed: {
    // css classes based on the change sign
    change () {
      const item = this.item
      const change = parseFloat(item.change)

      const symbol = {
        up: 'fa-arrow-up',
        down: 'fa-arrow-down',
        neutral: 'fa-minus'
      }

      const color = {
        green: 'has-text-success',
        red: 'has-text-danger',
        neutral: 'has-text-grey-light'
      }

      const animation = {
        up: 'bounceInUp',
        down: 'bounceInDown',
        neutral: 'flash'
      }

      if (change > 0) {
        if (item.type === 'Muscle') {
          return [symbol.up, color.green, animation.up].join(' ')
        } else {
          return [symbol.up, color.red, animation.up].join(' ')
        }
      } else if (change < 0) {
        if (item.type === 'Muscle') {
          return [symbol.down, color.red, animation.down].join(' ')
        } else {
          return [symbol.down, color.green, animation.down].join(' ')
        }
      }
      return [symbol.neutral, color.neutral, animation.neutral].join(' ')
    },

    // sets the tile classes
    tileClasses () {
      return this.setColors()
    }
  },

  methods: {
    // calculate the percentage change, increase or decrease to target goal
    calcPercentChange ({ current, target }) {
      return ((current - target) / target) * 100
    },

    // sets the color of the tile based on a threshold
    setColors () {
      let percent = this.calcPercentChange(this.item)

      if (percent <= this.threshold.low) {
        this.colorClass = 'is-success'
      } else if (percent <= this.threshold.medium) {
        this.colorClass = 'is-dark'
      } else if (percent <= this.threshold.high) {
        this.colorClass = 'is-warning'
      } else if (percent > this.threshold.high) {
        this.colorClass = 'is-danger'
      }

      return this.colorClass
    }
  }
}
</script>

<style lang="scss" scoped>
  @import "../assets/scss/colors";

  .goal {
    cursor: default;
  }

  .change-icon {
    animation-delay: 1s;
  }

  .has-text-success-darker {
    color: darken($success, 20%)
  }

  .has-text-warning-darker {
    color: darken($warning, 35%)
  }

  .has-text-danger-darker {
    color: darken($danger, 35%)
  }
</style>
