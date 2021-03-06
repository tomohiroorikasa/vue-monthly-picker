<template>
  <div class="vue-monthly-picker">
    <div class="month-picker-wrapper"
         :class="{ 'active visible':showMenu }">
      <div class="month-year-label picker" type="text" autocomplete="off" tabindex="0" @click="openMenu">
          <input @click="openMenu"
            class="form-control"
            :disabled="disabled"
            :value="displayText"
            @change="changeValue">
      </div>
      <div class="text"></div>
      <div class="date-popover" :class="menuClass" :style="menuStyle" tabindex="-1">
        <div class="picker" style="text-align: center">
          <div class="flexbox">
            <span class="prev" @click="prevYear" :class="{deactive: !canBack}"></span>
            <div>{{year}}</div>
            <span class="next" @click="nextYear" :class="{deactive: !canNext}"></span>
          </div>
          <div class="flexbox monthItem">
            <template v-for="(month, idx) in monthLabels">
              <div class="item active"
                :class="{'selected': isCurrentSelected(year, idx)}"
                v-if="isActive(idx)"
                @click="selectMonth(idx)"
                :key="idx">{{month}}
              </div>
              <div v-else
                :class="{'selected': isCurrentSelected(year, idx)}"
                :key="idx"
                class="item deactive">
                {{ month }}
              </div>
            </template>
          </div>
        </div>
      </div>
   </div>
  </div>
</template>

<script>
import dayjs from 'dayjs'

export default {
  name: 'vue-monthly-picker',
  props: {
    'value': {
      default: null
    },
    'disabled': {
      type: Boolean,
      default: false
    },
    'placeHolder': {
      type: String,
      default: ''
    },
    monthLabels: {
      type: Array,
      default: function () {
        return ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12']
      }
    },
    min: {
      default: null
    },
    max: {
      default: null
    },
    dateFormat: {
      type: String,
      default: 'YYYY/MM'
    }
  },
  data () {
    return {
      showMenu: false,
      year: dayjs().format('YYYY'),
      month: dayjs().format('MM')
    }
  },
  mounted () {
    this.init()
  },
  watch: {
    value: function (value) {
      this.setValue(value)
    },
    disabled: function () {
      this.setValue(this.value)
    }
  },
  computed: {
    menuClass () {
      return {
        visible: this.showMenu,
        hidden: !this.showMenu
      }
    },
    menuStyle () {
      return {
        display: this.showMenu ? 'block' : 'none'
      }
    },
    displayText () {
      if (this.value) {
        let valueDayjs = null
        if (typeof this.value === 'string') {
          valueDayjs = dayjs(this.value + '/1')
        } else {
          valueDayjs = this.value
        }
        if (valueDayjs && valueDayjs.isValid()) {
          return valueDayjs.format(this.dateFormat)
        }
      } else {
        return this.placeHolder
      }
    },
    canBack () {
      if (!this.min) return true
      const currentVal = this.internalDayjsValue.clone().startOf('year')
      return this.min.isBefore(currentVal)
    },
    canNext () {
      if (!this.max) return true
      const currentVal = this.internalDayjsValue.clone().endOf('year')
      return currentVal.isBefore(this.max)
    },
    internalDayjsValue () {
      const ym = this.year + '/' + this.month + '/1'
      return dayjs(ym, 'YYYY/MM')
    }
  },
  methods: {
    init () {
      document.addEventListener('click', (e) => {
        if (this.$el && !this.$el.contains(e.target)) {
          this.closeMenu()
        }
      }, false)
      this.setValue(this.value)
    },
    openMenu () {
      if (!this.disabled) {
        this.showMenu = true
      }
    },
    closeMenu () {
      this.showMenu = false
    },
    prevYear () {
      if (!this.canBack) return
      let newYear = parseInt(this.year) - 1
      this.year = newYear.toString()
    },
    nextYear () {
      if (!this.canNext) return
      let newYear = parseInt(this.year) + 1
      this.year = newYear.toString()
    },
    selectMonth (idx) {
      this.month = (parseInt(idx) + 1).toString()
      this.selectPicker()
      this.closeMenu()
    },
    selectPicker () {
      this.$emit('input', this.internalDayjsValue.format('YYYY/MM'))
      this.$emit('selected', this.internalDayjsValue.format('YYYY/MM'))
    },
    setValue (value) {
      if (value && typeof value === 'string') {
        value = dayjs(value)
      }
      if (value && value.isValid()) {
        this.month = value.format('MM')
        this.year = value.format('YYYY')
      } else {
        this.month = dayjs().format('MM')
        this.year = dayjs().format('YYYY')
      }
    },
    isActive (idx) {
      let realMonth = idx + 1
      const yrMonth = this.year + '/' + (realMonth < 10 ? '0' + realMonth : realMonth)
      if (this.min && dayjs(yrMonth, 'YYYY/MM').isBefore(this.min)) {
        return false
      }
      if (this.max && dayjs(yrMonth, 'YYYY/MM').isAfter(this.max)) {
        return false
      }
      return true
    },
    isCurrentSelected (year, monthIdx) {
      if (!this.value) {
        return false
      }
      let checkValue = this.value
      if (typeof this.value === 'string') {
        checkValue = dayjs(this.value)
      }
      if (checkValue && checkValue.isValid()) {
        const currentMonth = checkValue.format('MM')
        const currentYear = checkValue.format('YYYY')
        return Number(currentMonth) === Number(monthIdx + 1) && Number(currentYear) === Number(year)
      }
      return false
    },
    changeValue (elm) {
      if (elm.currentTarget.value) {
        let changeValue = dayjs(elm.currentTarget.value)

        if (changeValue.isValid()) {
          this.month = changeValue.format('MM')
          this.year = changeValue.format('YYYY')

          this.$emit('input', this.internalDayjsValue.format('YYYY/MM'))
          this.$emit('selected', this.internalDayjsValue.format('YYYY/MM'))
        }
      } else {
        this.$emit('input', '')
      }
    }
  }
}
</script>

<style lang="scss">
$lightgray: #d4d4d4;

.vue-monthly-picker {
  .picker {
    .next,
    .prev {
      &:hover {
        cursor: pointer;
      }
    }

    .monthItem {
      .item {
        border-top: 1px solid $lightgray;
        &.active {
          &:hover {
            cursor: pointer;
            background-color: $lightgray;
          }
        }
      }
    }

    .flexbox {
      padding: 0px;
      display: flex;
      flex-wrap: wrap;
      div {
        flex-grow: 1;
        padding: 15px 0;
      }
      .item {
        flex: 1;
        flex-basis: 25%;
      }
    }
  }

  .placeholder {
    color: #8b8b8b;
  }

  .date-popover {
    overflow-x: hidden;
    overflow-y: hidden;
    outline: none;
    max-width: 350px;
    width: 100%;
    border-radius: 0 0 .28571429rem .28571429rem;
    box-shadow: 0 2px 3px 0 rgba(34,36,38,.15);
    background: #fff;
    transition: opacity .1s ease;
    position: absolute;
    margin: auto;
    z-index: 10;
    border: 1px solid $lightgray;
    font-size: 1rem;
    font-weight: 200;
  }

  .month-picker-wrapper {
    position: relative;
    display: block;
    min-width: 200px;
  }

  .month-year-label {
    outline: none;
    .vmp-input-append {
      display: none;
    }
    &:hover .vmp-input-append {
      display: block;
    }
  }
  .text {
    position: relative;
    z-index: 2;
  }

  .next,
  .prev {
    width: 16%;
    float: left;
    text-indent: -10000px;
    position: relative;

    &:after {
      content: "";
      position: absolute;
      left: 50%;
      top: 50%;
      -webkit-transform: translateX(-50%) translateY(-50%);
      transform: translateX(-50%) translateY(-50%);
      border: 6px solid transparent;
    }
  }
  .next {
    &:after {
      border-left: 10px solid #000;
      margin-left: 5px;
    }
    &.deactive {
      &:hover {
        cursor: default;
      }
      &:after {
        border-left: 10px solid #999999;
      }
    }
  }

  .prev {
    &:after {
      border-right: 10px solid #000;
      margin-left: -5px;
    }
    &.deactive {
      &:hover {
        cursor: default;
      }
      &:after {
        border-right: 10px solid #999999;
      }
    }
  }

  .deactive {
    color: #999999;
  }

  .selected {
    color: #fff;
    background-color: #0062cc;
    font-weight: bold;
  }

  .vmp-input-append {
    position: absolute;
    top: 0;
    right: 0;
    width: 30px;
    height: 100%;
    padding: 6px;
  }
}
</style>
