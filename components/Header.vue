<script setup lang="ts">
import type { NavigationMenuItem } from '@nuxt/ui'

const items = ref<NavigationMenuItem[]>([
  {
    label: 'Blog',
    icon: 'i-lucide-book-open',
    to: 'https://cworld0.com/',
    target: '_blank',
  },
  {
    label: 'GitHub',
    icon: 'i-simple-icons-github',
    to: 'https://github.com/cworld1/ark-avatar',
    target: '_blank'
  },
])
</script>

<template>
  <div class="w-full flex justify-between items-center">
    <h1 class="font-bold not-lg:hidden">头像编辑器</h1>
    <UNavigationMenu :items="items" />
  </div>
</template>
