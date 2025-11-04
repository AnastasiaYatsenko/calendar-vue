<template>
  <div class="calendar-wrapper">
    <div class="calendar-header">
      <button class="button--arrow" @click="onMoveMonth('prev')" :disabled="mode !== 'day'">
        ◂
      </button>

      <div class="header-date-wrapper">
        <button @click="onChangeMode('month')">{{ currentMonth }}</button>

        <button @click="onChangeMode('year')">{{ currentYear }}</button>
      </div>

      <button class="button--arrow" @click="onMoveMonth('next')" :disabled="mode !== 'day'">
        ▸
      </button>
    </div>

    <div class="calendar-body">
      <div class="year-list" v-if="mode === 'year'">
        <button
          v-for="year in years"
          :key="year"
          :class="{ current: currentYear === year }"
          @click="onChangeDate('year', year)"
        >
          {{ year }}
        </button>
      </div>
      <div class="month-list" v-if="mode === 'month'">
        <button
          v-for="(month, index) in months"
          :key="month"
          :class="{ current: currentMonth === month }"
          @click="onChangeDate('month', index)"
        >
          {{ month }}
        </button>
      </div>
      <div class="day-list" v-if="mode === 'day'">
        <div class="day-item" v-for="weekday in weekdays">{{ weekday }}</div>
        <template v-for="day in days" :key="day || day?.day">
          <div v-if="day === null" class="day-item"></div>
          <button
            v-else
            class="day-item"
            :class="{
              today: dayjs().format('YYYY-MM-DD') === day.date,
              current: pickedDate === day.date,
            }"
            @click="onDayClick('date', day.day)"
          >
            {{ day.day }}
          </button>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import dayjs from '@/utils/dayjs'

const props = defineProps({
  initialDate: String,
  currentLocale: String,
})

const currentDate = ref(dayjs())
const pickedDate = ref()
const mode = ref('day')

const onChangeMode = (type) => {
  mode.value = type === mode.value ? 'day' : type
}

const onMoveMonth = (mode) => {
  const newDate =
    mode === 'next' ? currentDate.value.add(1, 'month') : currentDate.value.subtract(1, 'month')

  currentDate.value = newDate
}

const onChangeDate = (type, value) => {
  const newDate = currentDate.value.set(type, value)

  currentDate.value = newDate

  mode.value = 'day'
}

const onDayClick = async (type, value) => {
  onChangeDate(type, value)
  pickedDate.value = currentDate.value.format('YYYY-MM-DD')
  console.log(pickedDate.value)
}

const weekdays = computed(() => {
  const result = dayjs().locale(props.currentLocale).startOf('week')
  return Array.from({ length: 7 }, (item, index) => result.add(index, 'day').format('dd'))
})

const months = computed(() => {
  return Array.from({ length: 12 }, (item, index) =>
    dayjs().locale(props.currentLocale).month(index).format('MMMM'),
  )
})

const years = computed(() => {
  const currentYear = dayjs().year()
  return Array.from({ length: 20 }, (item, index) => String(currentYear - 10 + index))
})

const days = computed(() => {
  const start = currentDate.value.startOf('month')
  const end = currentDate.value.endOf('month')

  const offset = (start.day() + 6) % 7

  const result = Array.from({ length: offset }, () => null)

  for (let i = 1; i <= end.date(); i++) {
    const date = start.date(i).format('YYYY-MM-DD')
    result.push({ day: i, date })
  }

  while (result.length % 7 !== 0) {
    result.push(null)
  }

  return result
})

const currentMonth = computed(() => {
  return currentDate.value.locale(props.currentLocale).format('MMMM')
})

const currentYear = computed(() => {
  return currentDate.value.locale(props.currentLocale).format('YYYY')
})

onMounted(() => {
  const isInitialDateValid =
    props.initialDate && dayjs(props.initialDate, 'YYYY-MM-DD', true).isValid()

  if (isInitialDateValid) {
    currentDate.value = dayjs(props.initialDate)
    pickedDate.value = props.initialDate
  }
})
</script>

<style lang="css" scoped>
.calendar-wrapper {
  width: 232px;
  min-height: 232px;
  border: 1px solid gray;
  display: flex;
  flex-direction: column;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  padding: 5px;
}

.header-date-wrapper {
  display: flex;
  gap: 5px;
}

.button--arrow {
  font-size: 1.5rem;
  line-height: 1rem;
}

.calendar-body {
  padding: 0 5px 5px;
}

.month-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  align-items: center;
  gap: 10px;
}

.year-list {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

.day-list {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  column-gap: 2px;
}

.day-item {
  align-items: center;
  display: flex;
  justify-content: center;
  position: relative;
  height: 30px;
  width: 30px;
}
</style>
