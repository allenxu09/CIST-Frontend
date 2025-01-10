<template>
  <div class="min-h-screen bg-white flex items-center justify-center px-6 md:px-12">
    <div class="max-w-7xl w-full grid grid-cols-1 md:grid-cols-2 gap-12 items-center">
      <div class="flex justify-center">
        <div class="w-80 bg-gray-100 rounded-3xl shadow-lg p-6">
          <h1 class="text-2xl font-semibold text-gray-900 text-center mb-4">
            Search Preview
          </h1>
          <form @submit.prevent="search" class="flex flex-col gap-4">
            <input
              type="text"
              v-model="query"
              placeholder="Enter your search query..."
              class="w-full py-3 px-6 text-lg border border-gray-300 rounded-full focus:ring-2 focus:ring-blue-400 focus:outline-none shadow-sm"
              required
            />
            <button
              type="submit"
              class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-3 rounded-full shadow-lg transition-transform transform hover:scale-105"
            >
              Search
            </button>
          </form>
          <div v-if="loading" class="text-center text-gray-500 mt-4">Loading...</div>
          <div v-if="error" class="text-center text-red-500 mt-4">{{ error }}</div>
        </div>
      </div>
      <div>
        <h1 class="text-4xl md:text-5xl font-bold text-gray-900">
          Find The Idiom You're Looking For, Instantly.
        </h1>
        <button
          class="mt-6 bg-black hover:bg-gray-800 text-white font-semibold py-3 px-8 rounded-full shadow-md transition-transform transform hover:scale-105"
          @click="scrollToSearch"
        >
          Get Started
        </button>
      </div>
    </div>
  </div>

  <div id="results" class="mt-12 px-6 md:px-12">
    <div v-if="results.length">
      <h2 class="text-2xl font-semibold text-gray-800 mb-4">Search Results:</h2>
      <ul class="space-y-4">
        <li
          v-for="(result, index) in results"
          :key="index"
          class="p-4 bg-gray-100 rounded-xl shadow hover:bg-gray-200 transition cursor-pointer"
        >
          {{ result }}
        </li>
      </ul>
    </div>
    <div v-else-if="!loading && !error && results.length === 0">
      <p class="text-center text-gray-500 mt-6 text-lg">No results found.</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      query: "",
      results: [],
      loading: false,
      error: "",
    };
  },
  methods: {
    async search() {
      this.results = [];
      this.error = "";
      this.loading = true;

      try {
        const response = await axios.post(
            `http://localhost:8000/get/${this.query}`
        );
        this.results = response.data.results;
        this.$nextTick(() => {
          this.scrollToResults();
        });
      } catch (err) {
        this.error = "An error occurred while searching.";
        console.error(err);
      } finally {
        this.loading = false;
      }
    },
    scrollToSearch() {
      window.scrollTo({top: 0, behavior: "smooth"});
    },
    scrollToResults() {
      document.getElementById("results").scrollIntoView({
        behavior: "smooth",
        block: "start",
        inline: "nearest",
      });
    },
  },
};
</script>

<style>
html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Inter', sans-serif;
  transition: all 0.3s ease;
}
</style>