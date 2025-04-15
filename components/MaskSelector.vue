<template>
  <UCard>
    <template #header>遮罩设置</template>

    <!-- Style selection -->
    <div class="flex gap-4 overflow-x-auto">
      <div v-for="(mask, i) in masks" :key="i" class="rounded-lg overflow-hidden cursor-pointer m-1"
        :class="{ 'ring-2 ring-primary': mask === selectedMask }" @click="selectMask(mask)">
        <img :src="mask" class="w-20 h-20 object-cover" />
      </div>
    </div>

    <!-- Sliders -->
    <div class="mt-4 space-y-4 text-sm">
      <MaskSlider label="遮罩透明度" :min="0" :max="1" :step="0.05" v-model="local.opacity" />
      <MaskSlider label="缩放比例" :min="0.2" :max="2" :step="0.05" v-model="local.scale" />
      <MaskSlider label="水平位置 (X)" :min="-1" :max="1" :step="0.05" v-model="local.x" />
      <MaskSlider label="垂直位置 (Y)" :min="-1" :max="1" :step="0.05" v-model="local.y" />
      <MaskSlider label="底图亮度" :min="0" :max="200" :step="1" v-model="local.brightness" />
    </div>

    <UButton variant="outline" class="mt-6" @click="resetMaskSettings">重置遮罩参数</UButton>
  </UCard>
</template>

<script setup lang="ts">
import { ref, watch, toRefs } from 'vue'
import MaskSlider from './MaskSlider.vue'

const props = defineProps<{
  masks: string[]
  mask: string | null
  opacity: number
  scale: number
  x: number
  y: number
  brightness: number
}>()

const emit = defineEmits([
  'update:mask',
  'update:opacity',
  'update:scale',
  'update:x',
  'update:y',
  'update:brightness',
])

// Local state
const local = ref({
  opacity: props.opacity,
  scale: props.scale,
  x: props.x,
  y: props.y,
  brightness: props.brightness,
})

const selectedMask = ref(props.mask)

watch(() => props.mask, v => selectedMask.value = v)
watch(() => props.opacity, v => local.value.opacity = v)
watch(() => props.scale, v => local.value.scale = v)
watch(() => props.x, v => local.value.x = v)
watch(() => props.y, v => local.value.y = v)
watch(() => props.brightness, v => local.value.brightness = v)

// Emit changes to parent
watch(selectedMask, v => emit('update:mask', v))
watch(local.value, val => {
  emit('update:opacity', val.opacity)
  emit('update:scale', val.scale)
  emit('update:x', val.x)
  emit('update:y', val.y)
  emit('update:brightness', val.brightness)
}, { deep: true })

const selectMask = (mask: string) => {
  selectedMask.value = mask
}

const resetMaskSettings = () => {
  local.value = {
    opacity: 0.5,
    scale: 1,
    x: 0,
    y: 0,
    brightness: 100,
  }
}
</script>
