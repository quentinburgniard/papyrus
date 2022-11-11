<script setup>
  import { ref, watch } from 'vue';
  import Paragraph from './Paragraph.vue';

  const emit = defineEmits();
  const loading = ref(false);
  const props = defineProps(['token']);
  const localParagraphs = ref([]);
  const paragraphs = ref([]);

  watch(
    localParagraphs,
    (newLocalParagraphs, oldLocalParagraphs) => {
      console.log('watch');
      loading.value = true;
    },
    { deep: true }
  )

  function test(event) {
    localParagraphs.value.push({
      id: '1',
      attributes: {
        text: 'toto'
      }
    })
    sync();
  }

  function createLetter() {
    fetch('https://api.digitalleman.com/v2/letters', {
      headers: {
        'authorization': `Bearer ${props.token}`,
        'content-type': 'application/json'
      }
    })
    .then((response) => response.json())
    .then((data) => {
      if (data.error && data.error.status == 401) {
        //$emit('redirect');
      } else {
        paragraphs.value = data.data.paragraphs;
      }
    });
  }

  function sync() {
    createLetter();
  }
</script>

<template>
  <div class="position-relative" id="letter" ondragover="event.preventDefault()" style="min-height: 100px" :ondrop="test">
    <div class="bg-opacity-50 bg-white h-100 position-absolute w-100">
      <div class="position-absolute start-50 top-50 translate-middle" v-show="loading">
        <div class="spinner-border text-dark" role="status"></div>
      </div>
    </div>
    <Paragraph v-for="paragraph in localParagraphs" :key="paragraph.id" :paragraph="paragraph"/>
    <div class="text-right">
      <i class="bi bi-clipboard"></i>
    </div>
  </div>
</template>