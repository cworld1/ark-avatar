<template>
  <div class="flex flex-col gap-y-2">
    <label>{{ label }}: {{ modelValue.toFixed(2) }}</label>
    <USlider :min="min" :max="max" :step="step" v-model.number="internalValue" />
  </div>
</template>

<script setup lang="ts">
import { watch, ref } from 'vue'

const props = defineProps<{
  label: string
  modelValue: number
  min: number
  max: number
  step: number
}>()

const emit = defineEmits(['update:modelValue'])

const internalValue = ref(props.modelValue)

watch(() => props.modelValue, val => internalValue.value = val)
watch(internalValue, val => emit('update:modelValue', val))
</script>
