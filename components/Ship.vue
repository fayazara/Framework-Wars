<template>
  <div
    class="h-12 w-12 bg-white/10 rounded-full absolute bottom-4 left-1/2 -translate-x-1/2 flex items-center justify-center"
    :style="{ transform: `translateX(-50%) rotate(${rotation}deg)` }"
  >
    <Icon name="i-ph-paper-plane-duotone" class="text-white h-6 w-6" />
  </div>
</template>

<script setup>
defineProps({
  rotation: {
    type: Number,
    default: 0,
  },
});
</script>
