<template>
<div class="home">
  <img alt="Vue logo" src="../assets/logo.png">
  <autocomplete :items="posts" @input="load" />
</div>
</template>

<script>
// @ is an alias to /src
import Autocomplete from '@/components/Autocomplete.vue'

export default {
  name: 'home',
  components: {
    Autocomplete
  },

  data() {
    return {
      posts: [],
      errors: [],
    }
  },

  /*created() {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(response => response.json())
      .then(
        (json) => {
          console.log(json);
          this.posts = json;
          this.value = false;
        });
  }*/

  methods: {

    load(value) {
      console.log(value);

      fetch('https://jsonplaceholder.typicode.com/posts/?q=' + value)
        .then(response => response.json())
        .then(
          (json) => {
            console.log(json);
            this.posts = json;
          });


    }
  }

}
</script>
