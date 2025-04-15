<template>
  <!-- <div>
    <NuxtRouteAnnouncer />
    <NuxtWelcome />
  </div> -->
  <UApp>
    <UContainer class="py-6">
      <h1 class="text-2xl font-bold mb-6">图片裁剪编辑工具</h1>
      <ImageEditor />
    </UContainer>
  </UApp>
</template>

<script setup lang="ts">
import ImageEditor from '@/components/ImageEditor.vue'
</script>
