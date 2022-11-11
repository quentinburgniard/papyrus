<script setup>
  import { ref, watch } from 'vue';
  import Paragraph from './Paragraph.vue';

  const emit = defineEmits();
  const loading = ref(false);
  const props = defineProps(['token']);
  const letterID = ref(getLetterID() || '');
  const localParagraphs = ref([]);
  const paragraphs = ref([]);

  if (letterID.value) getLetter(letterID.value);

  /*watch(
    localParagraphs,
    (newLocalParagraphs, oldLocalParagraphs) => {
      console.log('watch');
      loading.value = true;
    },
    { deep: true }
  )*/

  function drop(event) {
    let ID = event.dataTransfer.getData('id');
    if (!localParagraphs.value.find(paragraph => paragraph.id == ID)) {
      localParagraphs.value.push({
        id: event.dataTransfer.getData('id'),
        attributes: {
          text: event.dataTransfer.getData('text')
        }
      })
      syncLetter(letterID);
    }
  }

  function getLetter(ID) {
    fetch('https://api.digitalleman.com/v2/letters/' + ID + '?populate=paragraphs', {
      headers: {
        'authorization': `Bearer ${props.token}`,
        'content-type': 'application/json'
      }
    })
    .then((response) => response.json())
    .then((data) => {
      if (data.error && [401, 403].includes(data.error.status)) {
        console.log('emit');
        emit('redirect');
      } else {
        paragraphs.value = data.data.attributes.paragraphs.data;
        localParagraphs.value = paragraphs.value;
        loading = false;
      }
    });
  }

  function getLetterID() {
    return localStorage.getItem('letter');
  }

  function syncLetter(ID) {
    ID = ID ? '/' + ID : '';
    fetch('https://api.digitalleman.com/v2/letters' + ID + '?populate=paragraphs', {
      body: JSON.stringify({
        data: {
          paragraphs: localParagraphs.value.map(paragraph => paragraph.id)
        }
      }),
      headers: {
        'authorization': `Bearer ${props.token}`,
        'content-type': 'application/json'
      },
      method: ID ? 'PUT' : 'POST'
    })
    .then((response) => response.json())
    .then((data) => {
      if (data.error && [401, 403].includes(data.error.status)) {
        emit('redirect');
      } else {
        localStorage.setItem('letter', data.data.id);
        paragraphs.value = data.data.attributes.paragraphs.data;
        loading.value = false;
      }
    })
  }
</script>

<template>
  <div class="position-relative" id="letter" ondragover="event.preventDefault()" style="min-height: 100px" :ondrop="drop">
    <div class="bg-opacity-50 bg-white h-100 position-absolute w-100" v-show="loading">
      <div class="position-absolute start-50 top-50 translate-middle">
        <div class="spinner-border text-dark" role="status"></div>
      </div>
    </div>
    <Paragraph v-for="paragraph in paragraphs" v-show="!loading" :key="paragraph.id" :paragraph="paragraph"/>
    <Paragraph v-for="paragraph in localParagraphs" v-show="loading" :key="paragraph.id" :paragraph="paragraph"/>
    <div class="text-right">
      <i class="bi bi-clipboard"></i>
    </div>
  </div>
</template>