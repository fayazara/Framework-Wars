<template>
  <div>
    <div
      v-for="word in words"
      :key="word.id"
      class="absolute text-white"
      :style="{ left: `${word.x}px`, top: `${word.y}px` }"
    >
      {{ word.word }}
    </div>
  </div>
</template>

<script setup>
defineProps({
  words: {
    type: Array,
    required: true,
  },
});
</script>
