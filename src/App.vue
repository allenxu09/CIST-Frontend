<template>
  <div class="min-h-screen bg-gray-100 flex flex-col">
    <AppHeader @request-random-idiom="getRandomIdiom" />

    <main class="container mx-auto px-4 py-6 sm:py-8 flex-grow w-full max-w-5xl">
      <SearchForm
        :initial-search-params="searchParams"
        :loading="loading && !searchResult"
        @search="handleSearch"
        class="mb-6"
      />

      <ErrorMessage :message="error" />

     <ResultsDisplay
        v-if="searchAttempted || loading"
        :search-result="searchResult"
        :limit="searchParams.limit"
        :offset="searchParams.offset"
        :loading="loading"
        @prev-page="prevPage"
        @next-page="nextPage"
        @show-detail="showIdiomDetail"
      />

       <!-- Placeholder/Instructions before first search -->
        <div v-else class="text-center py-16 px-6 bg-white rounded-xl shadow-md">
            <h2 class="text-2xl font-semibold text-gray-700 mb-3">开始查询成语</h2>
            <p class="text-gray-500">输入关键词，选择搜索类型，然后点击搜索按钮。</p>
            <button
                @click="getRandomIdiom"
                class="mt-6 bg-blue-100 text-blue-700 hover:bg-blue-200 font-medium px-5 py-2 rounded-lg transition text-sm"
            >
                或者查看一个随机成语
            </button>
        </div>

    </main>

    <!-- Use IdiomDetailModal component -->
    <IdiomDetailModal
      :show="showDetail"
      :idiom="selectedIdiom"
      @close="showDetail = false"
      @copy-idiom="handleCopyIdiom"
      ref="idiomModal"
    />

    <div v-if="globalLoading" class="fixed inset-0 bg-white bg-opacity-75 flex items-center justify-center z-60">
         <div class="inline-block animate-spin rounded-full h-10 w-10 border-t-2 border-b-2 border-blue-600"></div>
     </div>
  <AppFooter />
  </div>
</template>

<script>
import axios from "axios";
import AppHeader from './components/AppHeader.vue';
import SearchForm from './components/SearchForm.vue';
import ErrorMessage from './components/ErrorMessage.vue';
import ResultsDisplay from './components/ResultsDisplay.vue';
import IdiomDetailModal from './components/IdiomDetailModal.vue';
import AppFooter from './components/AppFooter.vue';

export default {
  components: {
    AppHeader,
    SearchForm,
    ErrorMessage,
    ResultsDisplay,
    IdiomDetailModal,
    AppFooter
  },
  data() {
    return {
      searchParams: {
        query: "",
        search_type: "mixed",
        exact_match: false,
        limit: 20,
        offset: 0
      },
      searchResult: null,
      loading: false,
      globalLoading: false,
      error: "",
      showDetail: false,
      selectedIdiom: {},
      searchAttempted: false,
      apiBaseUrl: "https://nutrimatic-chn.vercel.app"
    };
  },
  methods: {
    updateSearchParams(newParams) {
         this.searchParams = { ...this.searchParams, ...newParams };
    },
    handleSearch(paramsFromForm) {
        this.updateSearchParams({ ...paramsFromForm, offset: 0 });
        this.search();
    },
    async search() {
      if (!this.searchParams.query && this.searchParams.search_type !== 'regex') {
          this.error = "请输入查询内容。";
          return;
      }
      this.error = "";
      this.loading = true;
      this.searchAttempted = true; // Mark that a search has been tried
      this.searchResult = null; // Clear previous results immediately

      try {
        const response = await axios.post(
            `${this.apiBaseUrl}/search`,
            this.searchParams
        );
        this.searchResult = response.data;
        if (this.searchResult && this.searchParams.offset >= this.searchResult.total) {
             this.searchParams.offset = 0; // Go back to first page
         }
      } catch (err) {
        this.error = err.response?.data?.detail || "搜索失败，请检查网络或稍后再试。";
        this.searchResult = { total: 0, results: [] }; // Ensure results display shows empty state
        console.error("Search API Error:", err);
      } finally {
        this.loading = false;
      }
    },

    prevPage() {
      if (this.searchParams.offset > 0) {
        this.updateSearchParams({ offset: this.searchParams.offset - this.searchParams.limit });
        this.search(); // Refetch data for the previous page
      }
    },

    nextPage() {
      if (this.searchResult && this.searchParams.offset + this.searchParams.limit < this.searchResult.total) {
        this.updateSearchParams({ offset: this.searchParams.offset + this.searchParams.limit });
        this.search(); // Refetch data for the next page
      }
    },

    async fetchIdiomDetails(word) {
        this.globalLoading = true;
        this.error = "";
        try {
            const response = await axios.get(`${this.apiBaseUrl}/idiom/${word}`);
            this.selectedIdiom = response.data;
            this.showDetail = true;
        } catch (err) {
            this.error = `获取 "${word}" 的详情失败。`;
            this.selectedIdiom = { word: word }; // Keep the word at least
            console.error("Fetch Detail Error:", err);
        } finally {
            this.globalLoading = false;
        }
    },

    showIdiomDetail(idiom) {
      // If we already have full details from the search result (less likely now)
      if (idiom.explanation && idiom.pinyin && idiom.derivation !== undefined) { // Check more thoroughly
        this.selectedIdiom = idiom;
        this.showDetail = true;
      } else {
        // Fetch full details if needed
        this.fetchIdiomDetails(idiom.word);
      }
    },

    async getRandomIdiom() {
      this.globalLoading = true;
      this.error = "";
      try {
        const response = await axios.get(`${this.apiBaseUrl}/random`);
        this.selectedIdiom = response.data;
        this.showDetail = true;
      } catch (err) {
        this.error = "获取随机成语失败，请稍后再试。";
        this.selectedIdiom = {};
        console.error("Random Idiom Error:", err);
      } finally {
        this.globalLoading = false;
      }
    },

    handleCopyIdiom(text) {
      if (!text) return;
      navigator.clipboard.writeText(text)
          .then(() => {
             // Use the notification method from the modal component
             if (this.$refs.idiomModal && typeof this.$refs.idiomModal.triggerCopyNotification === 'function') {
                 this.$refs.idiomModal.triggerCopyNotification();
             }
            // Or use a global notification:
            // this.showCopySuccess = true;
            // setTimeout(() => this.showCopySuccess = false, 2000);
          })
          .catch(err => {
            console.error('Clipboard Write Error:', err);
            this.error = "复制失败。请检查浏览器权限或手动复制。";
          });
    }
  }
};
</script>

<style>
/* Global styles - Keep minimal or move to main.css/index.css */
body {
  font-family: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  background-color: #f3f4f6; /* Slightly different gray */
  height: 100%;
  margin: 0;
}

#app { /* Assuming your root element has id="app" */
    min-height: 100vh;
    display: flex;
    flex-direction: column;
}

/* Add smooth scrolling if desired */
/* html { scroll-behavior: smooth; } */
</style>