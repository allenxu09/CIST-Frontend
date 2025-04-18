<template>
  <div class="bg-white rounded-xl shadow-md p-5 sm:p-6">
    <form @submit.prevent="submitSearch" class="space-y-4">
      <!-- Search Input and Button -->
      <div class="flex flex-col sm:flex-row gap-3 sm:gap-4">
        <div class="flex-grow relative">
           <input
             v-model="localSearchParams.query"
             type="text"
             :placeholder="inputPlaceholder"
             class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none transition duration-150 ease-in-out text-base"
             required
             aria-label="查询内容"
           />
           <!-- Optional: Clear button -->
            <button type="button" v-if="localSearchParams.query" @click="clearQuery" class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600 p-1" title="清除输入">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
            </button>
        </div>

        <button
          type="submit"
          class="bg-blue-600 hover:bg-blue-700 text-white font-medium px-6 py-3 rounded-lg transition duration-150 ease-in-out flex items-center justify-center shrink-0 disabled:opacity-60 disabled:cursor-not-allowed"
          :disabled="loading || !localSearchParams.query"
        >
          <span v-if="loading" class="mr-2">
            <svg class="animate-spin h-5 w-5" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" fill="none"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
          </span>
          <span v-else>
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 hidden sm:inline-block" viewBox="0 0 20 20" fill="currentColor">
                <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" />
            </svg>
          </span>
          <span>搜索</span>
        </button>
      </div>

      <!-- Search Options -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 pt-2">
        <div>
          <label for="search_type" class="block text-sm font-medium text-gray-700 mb-1">搜索类型</label>
          <select
            id="search_type"
            v-model="localSearchParams.search_type"
            class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500 bg-white text-sm"
             @change="onSearchTypeChange"
          >
            <option value="mixed">混合 MIXED</option>
            <option value="regex">正则 (e.g., '^一.+一.$')</option>
            <option value="word">词语 (e.g., '一心一意')</option>
            <option value="explanation">释义 (e.g., '只有一个心思')</option>
          </select>
        </div>

        <div class="flex flex-col sm:flex-row sm:items-end sm:justify-between gap-4">
          <!-- Exact Match Checkbox -->
          <div class="pt-2 sm:pt-0 sm:self-end mb-1">
             <label class="inline-flex items-center cursor-pointer">
               <input type="checkbox" v-model="localSearchParams.exact_match" class="h-5 w-5 text-blue-600 rounded border-gray-300 focus:ring-blue-500">
               <span class="ml-2 text-sm text-gray-700">精确匹配</span>
             </label>
           </div>

          <!-- Limit Select -->
          <div class="sm:ml-4">
            <label for="limit" class="block text-sm font-medium text-gray-700 mb-1">每页结果</label>
            <select
              id="limit"
              v-model="localSearchParams.limit"
              class="border border-gray-300 rounded-lg px-3 py-2 focus:ring-blue-500 focus:border-blue-500 bg-white text-sm"
              @change="submitSearch"
            >
              <option :value="10">10</option>
              <option :value="20">20</option>
              <option :value="50">50</option>
              <option :value="100">100</option>
            </select>
          </div>
        </div>
      </div>
    </form>

    <!-- Regex Helper Integration -->
<!--    <transition name="fade-slide">-->
<!--        <RegexHelper-->
<!--            v-if="localSearchParams.search_type === 'regex'"-->
<!--            @apply-pattern="applyRegexPattern"-->
<!--            class="mt-5 border-t pt-4"-->
<!--        />-->
<!--    </transition>-->
  </div>
</template>

<script>

export default {
  name: 'SearchForm',
  props: {
    initialSearchParams: {
      type: Object,
      required: true
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  emits: ['search'],
  data() {
    return {
      localSearchParams: { ...this.initialSearchParams }
    };
  },
   computed: {
     inputPlaceholder() {
       switch (this.localSearchParams.search_type) {
         case 'mixed': return '输入混合表达式，见教程。'
         case 'regex': return '输入正则表达式, 如: ^一.+一.$';
         case 'word': return '输入成语, 如: 一心一意';
         case 'explanation': return '输入释义关键词, 如: 只有一个心思';
         default: return '请输入查询内容...';
       }
     }
   },
  watch: {
     initialSearchParams: {
       handler(newValue) {
         if (JSON.stringify(newValue) !== JSON.stringify(this.localSearchParams)) {
             this.localSearchParams = { ...newValue };
         }
       },
       deep: true,
     },
     'localSearchParams.limit': function(newLimit, oldLimit) {
       if (newLimit !== oldLimit) {
             // this.submitSearch();
         }
     }
  },
  methods: {
     clearQuery() {
        this.localSearchParams.query = '';

     },
    applyRegexPattern(pattern) {
      this.localSearchParams.query = pattern;
      this.submitSearch();
    },
    submitSearch() {
       const paramsToEmit = {
           query: this.localSearchParams.query,
           search_type: this.localSearchParams.search_type,
           exact_match: this.localSearchParams.exact_match,
           limit: Number(this.localSearchParams.limit)
       };
      this.$emit('search', paramsToEmit);
    },
     onSearchTypeChange() {
     }
  }
}
</script>

<style scoped>
.fade-slide-enter-active, .fade-slide-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.fade-slide-enter-from, .fade-slide-leave-to {
  opacity: 0;
  transform: translateY(10px);
}
</style>