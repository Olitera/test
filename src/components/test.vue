<script>
import axios from 'axios'
import Scichart2d from "./Scichart2d.vue";

export default {

  components: {
    Scichart2d
  },

  mounted() {
    // this.makeApiRequest();
  },

  data() {
    return {
      posts: null,
    }
  },

  methods: {

    getBasicAuthHeader() {
      const username = 'frontend2024';
      const password = 'test';
      const base64Credentials = btoa(`${username}:${password}`);
      return `Basic ${base64Credentials}`;
    },

    makeApiRequest() {
      const axiosConfig = {
        headers: {
          Authorization: this.getBasicAuthHeader(),
          'Content-Type': 'application/json',
        },
      };

      axios.get('https://rest.statica.pl/rest/stockquotes/gpw:PLKGHM000017?type=trades&dt_from=2010-01-01&limit=10000', axiosConfig)
          .then(response => {
            console.log('Response:', response.data);
          })
          .catch(error => {
            console.error('Error:', error.message);
          });
    },
  }
}

</script>
<template>
  <button @click="makeApiRequest">Get posts</button>
  <ul>
    <li v-for="post in posts" :key="post.id" data-test="post">
      {{ post.title }}
    </li>
  </ul>
  <Scichart2d msg="2D Charts" />
</template>
<style scoped>
</style>


