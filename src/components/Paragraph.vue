<script setup>
  import { ref } from 'vue';
  const emit = defineEmits();
  let selected = ref(false);
  let text = ref('');
</script>

<template>
  <div class="card" :class="{ 'border-primary': selected }" @click="selected = true">
    <div class="card-body" :class="{ 'text-primary': selected }">
      {{ text }}
    </div>
  </div>
</template>