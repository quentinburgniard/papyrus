<script setup>
  import { ref } from 'vue';
  const emit = defineEmits();
  defineProps(['paragraph']);

  function setDragData(event) {
    event.dataTransfer.setData('id', event.target.getAttribute('data-id'));
    event.dataTransfer.setData('text', event.target.getAttribute('data-text'));
  }
</script>

<template>
  <div class="p-2 m-2 border bg-light user-select-none" draggable="true" :data-id="paragraph.id" :data-text="paragraph.attributes.text" :ondragstart="setDragData">
    {{ paragraph.attributes.text }}
  </div>
</template>