<template>
  <svg
    width="40"
    height="5"
    :style="style"
    class="absolute"
    viewBox="0 0 40 5"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <path
      d="M38.5005 0.0624806C39.8706 0.0282291 40.9997 1.12951 40.9997 2.5V2.5C40.9997 3.87049 39.8706 4.97177 38.5005 4.93752L2.49922 4.03749C1.66511 4.01663 0.999746 3.33442 0.999746 2.5V2.5C0.999746 1.66558 1.66511 0.983366 2.49922 0.962512L38.5005 0.0624806Z"
      fill="url(#paint0_linear_172_9)"
    />
    <defs>
      <linearGradient
        id="paint0_linear_172_9"
        x1="35.5414"
        y1="2.5"
        x2="1"
        y2="2.5"
        gradientUnits="userSpaceOnUse"
      >
        <stop stop-color="white" />
        <stop offset="1" stop-color="white" stop-opacity="0" />
      </linearGradient>
    </defs>
  </svg>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  projectile: {
    type: Object,
    required: true,
  },
});

const style = computed(() => {
  const angle = Math.atan2(
    props.projectile.targetY - props.projectile.y,
    props.projectile.targetX - props.projectile.x
  );
  return {
    left: `${props.projectile.x}px`,
    top: `${props.projectile.y}px`,
    transform: `translate(-50%, -50%) rotate(${angle}rad)`,
  };
});
</script>
