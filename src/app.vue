<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h3>Vue Monthly Picker</h3>
    <fork-github githubUrl="https://github.com/tomohiroorikasa/vue-monthly-picker" />
    <div class="demo-component">
      <vue-monthly-picker
       :inputClass="{'input': isDisplayInput}"
       :disabled="isDisable"
       :monthLabels="locale"
       :min="min"
       :max="max"
       @selected="handleSelect"
       v-model="selectedMonth"
       :alignment="alignment"
       :selectedBackgroundColor="selectedBackgroundColor">
      </vue-monthly-picker>
    </div>
    <div class="container">
      <div class="row mt-5">
        <div class="col form-inline">
          <div class="form-group">
            <label for="Disabled" class="mr-2">Disabled:</label>
            <div class="form-check">
              <input class="form-check-input" type="checkbox" id="Disabled" v-model="isDisable">
              <!--label class="form-check-label" for="Disabled">Disabled</label-->
            </div>
          </div>
        </div>
        <div class="col form-inline">
          <div class="form-group">
            <label for="Locale" class="mr-2">Localization:</label>
            <select id="Locale" class="form-control" v-model="locale">
              <option
                  v-for="option in options"
                  :value="option.monthLabels"
                  :key="option.id">{{ option.title }}
              </option>
            </select>
          </div>
        </div>
        <div class="col form-inline">
          <div class="form-group">
            <label for="Color" class="mr-2">Selected background color:</label>
            <select id="Color" class="form-control" v-model="selectedBackgroundColor">
              <option
                  v-for="color in colorExamples"
                  :value="color"
                  :key="color">
                  {{ color }}
              </option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import dayjs from 'dayjs'
import VueMonthlyPicker from './lib'
import ForkGithub from './common/ForkGithub.vue'

export default {
  name: 'app',
  data () {
    return {
      selectedMonth: dayjs(),
      isDisable: false,
      isDisplayInput: true,
      locale: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
      alignment: 'left',
      selectedBackgroundColor: 'blue',
      options: [
        {
          id: 1,
          title: 'English',
          monthLabels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
        },
        {
          id: 2,
          title: 'Japanese',
          monthLabels: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
        },
        {
          id: 3,
          title: 'Korean',
          monthLabels: ['1월', '2월', '3월', '4월', '5월', '6월', '7월', '8월', '9월', '10월', '11월', '12월']
        }
      ],
      alignments: ['left', 'center', 'right'],
      colorExamples: ['blue', 'red', 'black'],
      min: null,
      max: null
    }
  },
  methods: {
    handleSelect (value) {
      console.log('Select', value)
    },
    setSelectRange (min, max) {
      this.min = min
      this.max = max
    }
  },
  components: {
    VueMonthlyPicker,
    ForkGithub
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.demo-component {
  width: 250px;
  margin: auto;
}
.control-group {
  margin-top: 100px;
}
.control {
  text-align: center !important;
}
.option-list {
  margin-top: 20px;
}
</style>
