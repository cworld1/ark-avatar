<template>
  <UContainer class="py-6 space-y-8 max-w-4xl mx-auto">
    <div class="flex flex-col items-center gap-y-2">
      <h1 class="text-3xl font-bold">PRTS 头像编辑器</h1>
      <p class="text-gray-500">上传图片，裁剪、添加遮罩，并导出你的专属头像</p>
      <div class="flex gap-x-2">
        <UButton as="label" variant="solid" color="primary">
          上传图片
          <input type="file" accept="image/*" class="hidden" @change="onFileChange" />
        </UButton>
        <UButton class="not-lg:hidden" color="primary" v-if="imageUrl" @click="saveImage">保存图片</UButton>
      </div>
    </div>

    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

      <!-- Mask Settings -->
      <div class="space-y-6">
        <MaskSelector :masks="masks" v-model:mask="selectedMask" v-model:opacity="maskOpacity" v-model:scale="maskScale"
          v-model:x="maskX" v-model:y="maskY" v-model:brightness="maskBrightness" />
      </div>

      <UCard>
        <template #header>裁剪 & 预览</template>
        <div class="flex flex-col gap-y-4 lg:gap-y-6">
          <vue-cropper ref="cropper" v-if="imageUrl" :src="imageUrl" :aspect-ratio="1" :drag-mode="'move'"
            :autoCrop="true" :background="false" @crop="renderPreview" @ready="renderPreview" @zoom="renderPreview"
            @cropmove="renderPreview" />
          <canvas ref="previewCanvas"
            class="max-w-full rounded-lg bg-black not-lg:fixed not-lg:bottom-4 not-lg:left-4 not-lg:right-4 not-lg:ring-2 not-lg:ring-gray-500 not-lg:max-h-50" />
        </div>
      </UCard>
    </div>

    <UButton class="float-end lg:hidden" color="primary" v-if="imageUrl" @click="saveImage">保存图片</UButton>
  </UContainer>
</template>

<script setup lang="ts">
import { ref, watch, nextTick } from 'vue'
import VueCropper from 'vue-cropperjs'
import 'cropperjs/dist/cropper.css'
import MaskSelector from './MaskSelector.vue'

const masks = [
  '/masks/mask1.png',
  // ...
]

const imageUrl = ref<string | null>(null)
const cropper = ref<any>(null)
const previewCanvas = ref<HTMLCanvasElement | null>(null)

const selectedMask = ref<string | null>(null)
const maskOpacity = ref<number>(0.5)
const maskScale = ref<number>(1)
const maskX = ref<number>(0)
const maskY = ref<number>(0)
const maskBrightness = ref<number>(100)

const loadedMasks: Record<string, HTMLImageElement> = {}

function getMaskImage(src: string): Promise<HTMLImageElement> {
  return new Promise((resolve, reject) => {
    if (loadedMasks[src]) return resolve(loadedMasks[src])
    const img = new Image()
    img.crossOrigin = 'anonymous'
    img.onload = () => {
      loadedMasks[src] = img
      resolve(img)
    }
    img.onerror = reject
    img.src = src
  })
}

async function renderPreview() {
  await nextTick()
  if (!cropper.value || !previewCanvas.value) return

  const srcCanvas = cropper.value.getCroppedCanvas()
  const width = srcCanvas.width
  const height = srcCanvas.height

  const offscreen = document.createElement('canvas')
  offscreen.width = width
  offscreen.height = height
  const offCtx = offscreen.getContext('2d')
  if (!offCtx) return

  // Step 1: 底图亮度调整
  offCtx.filter = `brightness(${maskBrightness.value}%)`
  offCtx.drawImage(srcCanvas, 0, 0)
  offCtx.filter = 'none'

  // Step 2: 遮罩处理
  if (selectedMask.value) {
    try {
      const maskImg = await getMaskImage(selectedMask.value)
      const scaledWidth = width * maskScale.value
      const scaledHeight = height * maskScale.value
      const offsetX = width / 2 - scaledWidth / 2 + maskX.value * width
      const offsetY = height / 2 - scaledHeight / 2 + maskY.value * height

      const intermediateCanvas = document.createElement('canvas')
      intermediateCanvas.width = width
      intermediateCanvas.height = height
      const iCtx = intermediateCanvas.getContext('2d')!

      iCtx.fillStyle = 'rgba(0, 0, 0, 0.77)'
      iCtx.fillRect(0, 0, width, height)
      iCtx.globalCompositeOperation = 'destination-out'
      iCtx.drawImage(maskImg, offsetX, offsetY, scaledWidth, scaledHeight)
      iCtx.globalCompositeOperation = 'source-over'
      iCtx.drawImage(maskImg, offsetX, offsetY, scaledWidth, scaledHeight)
      iCtx.globalAlpha = 1
      offCtx.globalAlpha = maskOpacity.value
      offCtx.drawImage(intermediateCanvas, 0, 0)
    } catch (err) {
      console.error('遮罩加载失败:', err)
    }
  }

  const mainCtx = previewCanvas.value.getContext('2d')
  if (!mainCtx) return

  previewCanvas.value.width = width
  previewCanvas.value.height = height
  mainCtx.clearRect(0, 0, width, height)
  mainCtx.drawImage(offscreen, 0, 0)
}

function onFileChange(e: Event) {
  const file = (e.target as HTMLInputElement).files?.[0]
  if (file && file.type.startsWith('image/')) {
    imageUrl.value = URL.createObjectURL(file)
  } else {
    alert('请上传有效的图片文件。')
  }
}

function saveImage() {
  renderPreview()
  setTimeout(() => {
    if (!previewCanvas.value) return
    const link = document.createElement('a')
    link.download = 'avatar.png'
    link.href = previewCanvas.value.toDataURL('image/png')
    link.click()
  }, 200)
}

watch(imageUrl, () => {
  if (!selectedMask.value && masks.length > 0) {
    selectedMask.value = masks[0]
  }
})
watch([selectedMask, maskOpacity, maskScale, maskX, maskY, maskBrightness], renderPreview)
</script>
