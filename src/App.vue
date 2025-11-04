<script setup>
import CalendarItem from '@/components/CalendarItem.vue'
import LangSwitcher from '@/components/LangSwitcher.vue'

import dayjs from '@/utils/dayjs'
import { ref, onMounted } from 'vue'

const currentLocale = ref(dayjs.locale())
const initialDate = ref('2025-11-05')

const onChangeLocale = (locale) => {
  currentLocale.value = locale
  dayjs.locale(locale)
}

onMounted(() => {
  const locale = (navigator.language || navigator.userLanguage).split('-')[0]
  onChangeLocale(locale)
})
</script>

<template>
  <div class="wrapper">
    <lang-switcher v-model="currentLocale" @update:model-value="onChangeLocale" />
    <calendar-item :current-locale="currentLocale" :initial-date="initialDate" />
  </div>
</template>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
