<template>
  <!-- <div>
    <NuxtRouteAnnouncer />
    <NuxtWelcome />
  </div> -->
  <UApp>
    <UContainer class="py-6">
      <Header />
      <ImageEditor />
    </UContainer>
  </UApp>
</template>

<script setup lang="ts">
import ImageEditor from '@/components/ImageEditor.vue'
import Header from '@/components/Header.vue'
</script>
