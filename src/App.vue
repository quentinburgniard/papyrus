<script setup>
  import { computed, ref, watch } from 'vue';
  import Paragraph from './components/Paragraph.vue';
  let text = ref('');
  let validated = ref(false);

  function createParagraph() {
    if (text.value) {
      fetch('https://api.digitalleman.com/v2/paragraphs', {
        body: JSON.stringify({
          text: text.value
        }),
        headers: {
          'authorization': `Bearer ${token()}`,
          'content-type': 'application/json'
        },  
        method: 'POST'
      })
      .then((response) => response.json())
      .then((data) => {
        if (data.error) {
          redirect();
        }
      });   
    } else {
      validated.value = true;
    }
  }

  function getParagraphs() {
    fetch('https://api.digitalleman.com/v2/paragraphs', {
      headers: {
        'authorization': `Bearer ${token()}`,
        'content-type': 'application/json'
      }
    })
    .then((response) => response.json())
    .then((data) => {
      console.log(data);
      if (data.error) {
        //redirect();
      }
    });
  }

  function redirect() {
    window.location.href = 'https://id.digitalleman.com?r=127.0.0.1%3A5173';
  }

  function token() {
    let match = document.cookie.match(/t=([^;]+)/);
    if (match && match[1]) {
      return match[1];
    } else {
      redirect();
    }
  }
</script>

<template>
  <Paragraph/>
  <form class="needs-validation" :class="{ 'was-validated': validated }" novalidate @submit.prevent="createParagraph">
    <div class="mt-3">
      <label class="form-label" for="text">Texte</label>
      <textarea class="form-control" id="text" placeholder="Paragraphe" required v-model.lazy="text"></textarea>
      <div class="invalid-feedback">
        Texte est requis.
      </div>
    </div>
    <div class="mt-3">
      <button class="btn btn-primary" type="submit">Ajouter un paragraphe</button>
    </div>
  </form>
</template>