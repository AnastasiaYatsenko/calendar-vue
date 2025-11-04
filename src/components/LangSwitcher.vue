<template>
  <div class="buttons-wrapper">
    <button
      v-for="locale in localesList"
      :key="locale"
      @click="emit('update:modelValue', locale)"
      :class="{ current: locale === modelValue }"
    >
      {{ locale }}
    </button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import dayjs, { localesList } from '@/utils/dayjs'

const props = defineProps({
  modelValue: String,
})

const emit = defineEmits(['update:modelValue'])
</script>

<style lang="css">
.buttons-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 5px;
  padding-bottom: 10px;
}
</style>
