<template>
  <div id="form">
    <form @submit.prevent="shortenUrl">
      <input v-model="longUrl" type="text" placeholder="Enter the URL..." />
      <button type="submit" :disabled="loading">Shorten</button>
      <LoadingSpinner v-if="loading" />
      <div v-if="shortUrl" class="result">
        Short URL: <a :href="shortUrl" target="_blank">{{ shortUrl }}</a>
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";
import { ref } from "vue";
import LoadingSpinner from "./LoadingSpinner.vue";

export default {
  name: "ShortMeForm",
  components: {
    LoadingSpinner,
  },
  setup() {
    const longUrl = ref("");
    const shortUrl = ref("");
    const loading = ref(false);

    const shortenUrl = async () => {
      loading.value = true;
      try {
        const response = await axios.post(
          "http://shortme-api.leonardo.ramos:8081",
          { longUrl: longUrl.value },
          {
            headers: {
              "X-API-KEY": "batatadoce",
            },
          }
        );
        shortUrl.value = response.data.shortUrl;
      } catch (error) {
        console.error("Error shortening URL:", error);
      } finally {
        loading.value = false;
      }
    };

    return {
      longUrl,
      shortUrl,
      loading,
      shortenUrl,
    };
  },
};
</script>

<style scoped>
#form {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

#form input {
  padding: 10px;
  width: 500px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.result {
  margin-top: 20px;
  font-size: 16px;
}
</style>
