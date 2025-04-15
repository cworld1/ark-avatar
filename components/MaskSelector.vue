<template>
  <UCard>
    <template #header>遮罩设置</template>

    <!-- 遮罩选择 -->
    <div class="flex gap-4 overflow-x-auto">
      <div v-for="(mask, i) in masks" :key="i" class="border rounded cursor-pointer p-1"
        :class="{ 'ring-2 ring-primary': localMask === mask }" @click="select(mask)">
        <img :src="mask" class="w-20 h-20 object-cover" />
      </div>
    </div>

    <!-- 设置项 -->
    <div class="mt-4 space-y-4 text-sm">
      <div class="flex flex-col space-y-2">
        <label>遮罩透明度: {{ localOpacity.toFixed(2) }}</label>
        <USlider :min="0" :max="1" :step="0.05" v-model.number="localOpacity" class="w-full" />
      </div>
      <div class="flex flex-col gap-y-2">
        <label>缩放比例: {{ localScale.toFixed(2) }}</label>
        <USlider :min="0.2" :max="2" :step="0.05" v-model.number="localScale" class="w-full" />
      </div>
      <div class="flex flex-col gap-y-2">
        <label>水平位置 (X): {{ localX.toFixed(2) }}</label>
        <USlider :min="-1" :max="1" :step="0.05" v-model.number="localX" class="w-full" />
      </div>
      <div class="flex flex-col gap-y-2">
        <label>垂直位置 (Y): {{ localY.toFixed(2) }}</label>
        <USlider :min="-1" :max="1" :step="0.05" v-model.number="localY" class="w-full" />
      </div>
      <div class="flex flex-col gap-y-2">
        <label>底图亮度: {{ maskBrightness.toFixed(2) }}</label>
        <USlider :min="0" :max="200" :step="1" v-model.number="maskBrightness" class="w-full" />
      </div>
    </div>
  </UCard>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

const props = defineProps<{
  mask: string | null
  opacity: number
  scale: number
  x: number
  y: number
  brightness: number // brightness 属性
}>()

const emit = defineEmits([
  'update:mask',
  'update:opacity',
  'update:scale',
  'update:x',
  'update:y',
  'update:brightness' // brightness 更新事件
])

const masks = [
  '/masks/mask1.png',
  // 更多样式可继续添加
]

const localMask = ref(props.mask)
const localOpacity = ref(props.opacity)
const localScale = ref(props.scale)
const localX = ref(props.x)
const localY = ref(props.y)
const maskBrightness = ref(props.brightness ?? 100) // 设置默认值

watch(() => props.mask, v => localMask.value = v)
watch(() => props.opacity, v => localOpacity.value = v)
watch(() => props.scale, v => localScale.value = v)
watch(() => props.x, v => localX.value = v)
watch(() => props.y, v => localY.value = v)
watch(() => props.brightness, v => maskBrightness.value = v)

watch(localMask, val => emit('update:mask', val))
watch(localOpacity, val => emit('update:opacity', val))
watch(localScale, val => emit('update:scale', val))
watch(localX, val => emit('update:x', val))
watch(localY, val => emit('update:y', val))
watch(maskBrightness, val => emit('update:brightness', val))

watch(imageUrl, () => {
  if (!selectedMask.value && masks.length > 0) {
    selectedMask.value = masks[0]
  }
})

const select = (mask: string) => {
  localMask.value = mask
}
</script>
