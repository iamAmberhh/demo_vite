<template>
  <div class="about">
    <h1>This is an about page</h1>
    {{ data }}
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      data: 123,
    };
  },
  mounted() {
    console.log(import.meta.env.VITE_TEST);
    axios
      .get("https://randomuser.me/api/")
      .then((res) => (this.data = res.data.results[0]))
      .catch((err) => console.log(err));
  },
};
</script>
