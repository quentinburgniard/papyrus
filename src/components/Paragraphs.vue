<script setup>
  import { reactive, ref } from 'vue';
  import Paragraph from './Paragraph.vue';

  const emit = defineEmits();
  const props = defineProps(['token']);
  let paragraphs = ref([]);

  getParagraphs();

  function getParagraphs() {
    fetch('https://api.digitalleman.com/v2/paragraphs', {
      headers: {
        'authorization': `Bearer ${props.token}`,
        'content-type': 'application/json'
      }
    })
    .then((response) => response.json())
    .then((data) => {
      if (data.error && data.error.status == 401) {
        $emit('redirect');
      } else {
        paragraphs.value = data.data;
      }
    });
  }
</script>

<template>
  <div id="paragraphs" style="min-height: 100px">
    <Paragraph v-for="paragraph in paragraphs" :key="paragraph.id" :paragraph="paragraph"/>
  </div>
</template>