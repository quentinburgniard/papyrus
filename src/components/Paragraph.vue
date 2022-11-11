<script setup>
  import { ref } from 'vue';
  const emit = defineEmits();
  defineProps(['paragraph']);
</script>

<template>
  <div class="p-2 m-2 border bg-light user-select-none" draggable="true" :data-id="paragraph.id">
    {{ paragraph.attributes.text }}
  </div>
</template>