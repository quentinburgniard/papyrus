<script setup>
  import { computed, ref, watch } from 'vue';
  import Letter from './components/Letter.vue';
  import Paragraphs from './components/Paragraphs.vue';
  
  let text = ref('');
  let token = ref(getToken());
  let validated = ref(false);

  function createParagraph() {
    if (text.value) {
      fetch('https://api.digitalleman.com/v2/paragraphs', {
        body: JSON.stringify({
          data: {
            text: text.value
          }
        }),
        headers: {
          'authorization': `Bearer  ${token.value}`,
          'content-type': 'application/json'
        },
        method: 'POST'
      })
      .then((response) => response.json())
      .then((data) => {
        if (data.error && data.error.status == 401) {
          //redirect();
        }
      });   
    } else {
      validated.value = true;
    }
  }

  function redirect() {
    window.location.href = 'https://id.digitalleman.com?r=http%3A%2F%2F127.0.0.1%3A5173';
  }

  function getToken() {
    let match = document.cookie.match(/t=([^;]+)/);
    if (match && match[1]) {
      return match[1];
    } else {
      redirect();
    }
  }
</script>

<template>
  <div class="container">
    <div class="row gx-5">
      <div class="col">
        <Paragraphs :token="token" @redirect="redirect"/>
        <form class="needs-validation" :class="{ 'was-validated': validated }" novalidate @submit.prevent="createParagraph">
          <div>
            <label class="form-label" for="text">Texte</label>
            <textarea class="form-control" id="text" placeholder="Paragraphe" required v-model.lazy="text"></textarea>
            <div class="invalid-feedback">
              Texte est requis.
            </div>
          </div>
          <div>
            <button class="btn btn-primary" type="submit">Ajouter un paragraphe</button>
          </div>
        </form>
      </div>
      <div class="col">
        <Letter/>
      </div>
    </div>
  </div>

</template>