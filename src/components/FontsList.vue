<script setup lang="ts">
import type { PostListFont } from 'src/types/type';

type Props = {
  fontsPosts: PostListFont[]
}

const { fontsPosts } = defineProps<Props>()

const onMounted = () => {
  // console.log(fontsPosts)
}
</script>

<template>

<ul class="fontList">
      <li class="fontList-item" v-for="font in fontsPosts" :key="font.id">
        <div class="image">
          <NuxtLinkLocale :to="`/fonts/${font.slug}`">
          <NuxtImg :src="`${font.thumbnail.url}`" loading="lazy" format="webp" :alt="`${font.title.rendered}`" />
        </NuxtLinkLocale>
        </div>
        <p class="fontsList-item-name">{{ font.title.rendered }}</p>
      </li>
  </ul>
</template>

<style lang="scss" scoped>

</style>
