<template>
  <div v-if="searchResult" class="bg-white rounded-xl shadow-lg p-6 mt-8">
    <!-- Header and Pagination -->
    <div class="flex flex-col sm:flex-row justify-between items-center mb-6 pb-4 border-b border-gray-200">
      <h3 class="text-lg font-semibold text-gray-800 mb-3 sm:mb-0">
        搜索结果 (共 {{ searchResult.total }} 条)
      </h3>
      <div v-if="totalPages > 1" class="flex items-center space-x-2">
        <button
          @click="$emit('prev-page')"
          :disabled="currentPage === 1"
          class="px-4 py-2 border rounded-md text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed transition"
        >
          上一页
        </button>
        <span class="text-gray-600 text-sm">
          第 {{ currentPage }} 页 / 共 {{ totalPages }} 页
        </span>
        <button
          @click="$emit('next-page')"
          :disabled="currentPage === totalPages"
          class="px-4 py-2 border rounded-md text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed transition"
        >
          下一页
        </button>
      </div>
    </div>

    <!-- Loading State -->
    <div v-if="loading" class="text-center py-10">
      <div class="inline-block animate-spin rounded-full h-8 w-8 border-t-2 border-b-2 border-blue-600"></div>
      <p class="text-gray-500 mt-2">加载中...</p>
    </div>

    <!-- No Results Message -->
    <div v-else-if="searchResult.results.length === 0" class="text-center py-10 text-gray-500">
      <svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      <p class="mt-2 text-lg">未找到匹配的成语</p>
      <p class="text-sm">请尝试其他关键词或搜索类型。</p>
    </div>

    <!-- Results Grid -->
    <div v-else class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <div
        v-for="(idiom) in searchResult.results"
        :key="idiom.word"
        class="border border-gray-200 rounded-lg p-4 hover:shadow-lg hover:border-blue-300 transition cursor-pointer bg-white"
        @click="$emit('show-detail', idiom)"
      >
        <h4 class="text-xl font-medium text-blue-700">{{ idiom.word }}</h4>
        <p class="text-gray-500 text-sm mt-1">{{ idiom.pinyin }}</p>
        <p class="text-gray-600 mt-2 text-sm line-clamp-2">{{ idiom.explanation }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ResultsDisplay',
  props: {
    searchResult: {
      type: Object,
      default: null
    },
    limit: {
      type: Number,
      required: true
    },
    offset: {
        type: Number,
        required: true
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  emits: ['prev-page', 'next-page', 'show-detail'],
  computed: {
    currentPage() {
      if (!this.searchResult || this.limit <= 0) return 1;
      return Math.floor(this.offset / this.limit) + 1;
    },
    totalPages() {
      if (!this.searchResult || !this.searchResult.total || this.limit <= 0) return 1;
      return Math.ceil(this.searchResult.total / this.limit);
    }
  }
}
</script>

<style scoped>
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
