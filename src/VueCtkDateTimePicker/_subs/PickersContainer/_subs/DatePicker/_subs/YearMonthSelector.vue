<template>
  <div
    class="year-month-selector flex flex-direction-column"
    :class="{'dark': dark}"
  >
    <div class="flex header-controls">
      <div v-if="!isMonthMode" class="year-controls">
        <div
          class="year-control"
          @click="prevYears"
        >
          <svg
            width="512"
            height="512"
            viewBox="0 0 512 512"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M338 419L175 256L338 93"
              stroke="black"
              stroke-width="24"
              stroke-linecap="round"
            />
          </svg>
        </div>
        <div
          class="year-control"
          @click="nextYears"
        >
          <svg
            width="512"
            height="512"
            viewBox="0 0 512 512"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M175 419L338 256L175 93"
              stroke="black"
              stroke-width="24"
              stroke-linecap="round"
            />
          </svg>
        </div>
      </div>
      <CustomButton
        :color="dark ? '#757575' : '#424242'"
        :dark="dark"
        with-border
        @click="$emit('back')"
      >
        <span class="fs-16">
          ✕
        </span>
      </CustomButton>
    </div>
    <div class="flex-1 flex flex-wrap justify-content-between align-center">
      <CustomButton
        v-for="(m, index) in months"
        :key="index"
        :color="color"
        :selected="currentMonth === index"
        :dark="dark"
        class="month-button"
        with-border
        @click="selectMonth(index)"
      >
        {{ m }}
      </CustomButton>
      <CustomButton
        v-for="year in years"
        :key="year"
        :color="color"
        :dark="dark"
        :selected="currentYear === year"
        with-border
        @click="selectYear(year)"
      >
        {{ year }}
      </CustomButton>
    </div>
  </div>
</template>

<script>
  import { getMonthsShort } from '@/VueCtkDateTimePicker/modules/month'
  import CustomButton from '@/VueCtkDateTimePicker/_subs/CustomButton'

  const ArrayRange = (start, end) => {
    return Array(end - start + 1).fill().map((_, idx) => {
      const n = start + idx
      return n
    })
  }

  export default {
    name: 'YearMonthSelector',
    components: {
      CustomButton
    },
    props: {
      locale: { type: String, default: null },
      dark: { type: Boolean, default: null },
      color: { type: String, default: null },
      mode: { type: String, default: null },
      month: { type: Object, default: null }
    },
    emits: ['input', 'back'],
    data () {
      return {
        months: null,
        years: null
      }
    },
    computed: {
      currentMonth () {
        return this.month.month
      },
      currentYear () {
        return this.month.year
      },
      isMonthMode () {
        return this.mode === 'month'
      }
    },
    mounted () {
      if (this.isMonthMode) {
        this.getMonths()
      } else {
        this.getYears()
      }
    },
    methods: {
      getMonths () {
        this.years = null
        this.months = getMonthsShort(this.locale)
      },
      getYears () {
        this.months = null
        this.years = ArrayRange(this.month.year - 7, this.month.year + 7)
      },
      selectMonth (monthNumber) {
        this.$emit('input', { month: monthNumber, year: this.currentYear })
      },
      selectYear (year) {
        this.$emit('input', { month: this.currentMonth, year: year })
      },
      prevYears () {
        const firstYear = this.years[0]
        this.years = ArrayRange(firstYear - 7, firstYear + 7)
      },
      nextYears () {
        const lastYear = this.years[this.years.length - 1]
        this.years = ArrayRange(lastYear - 7, lastYear + 7)
      }
    }
  }
</script>

<style lang="scss" scoped>
  .year-month-selector{
    position: absolute;
    background-color: white;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    color: #424242;
    padding: 10px;
    &.dark {
      color: white;
      background-color: #424242;
    }
    .month-button {
      text-transform: capitalize;
    }
  }
  .header-controls {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .year-controls {
    display: flex;
  }
  .year-control {
    cursor: pointer;
    width: 14px;
    height: 14px;
    svg {
      width: 100%;
      height: 100%;
    }
  }
</style>
