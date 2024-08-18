<template>
  <div id="form">
    <input v-model="longUrl" type="text" placeholder="Enter the URL..." />
    <button @click="shortenUrl">Shorten</button>
    <LoadingSpinner v-if="loading" />
    <div v-if="shortUrl" class="result">
      Short URL: <a :href="shortUrl" target="_blank">{{ shortUrl }}</a>
    </div>
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
    const apiEndpoint = process.env.VUE_APP_API_URL;
    const apiPort = process.env.VUE_APP_API_PORT;
    const apiKey = process.env.VUE_APP_API_KEY;

    const shortenUrl = async () => {
      loading.value = true;
      try {
        const response = await axios.post(
          apiEndpoint + ":" + apiPort,
          { longUrl: longUrl.value },
          {
            headers: {
              "x-api-key": apiKey,
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
