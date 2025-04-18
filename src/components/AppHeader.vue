<template>
  <nav class="bg-gradient-to-r from-blue-700 to-blue-800 text-white shadow-lg sticky top-0 z-40">
    <div class="container mx-auto px-4 py-3 flex items-center justify-between max-w-5xl">
      <h1 class="text-2xl font-bold tracking-tight">成语查询</h1>
      <div class="flex items-center space-x-3">
        <button
          @click="goToTutorial"
          class="bg-white bg-opacity-20 hover:bg-opacity-30 text-white font-medium px-4 py-2 rounded-lg transition duration-150 ease-in-out text-sm flex items-center space-x-2"
          title="查看使用教程"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253" />
          </svg>
          <span>使用教程</span>
        </button>
        <button
          @click="$emit('request-random-idiom')"
          class="bg-white bg-opacity-20 hover:bg-opacity-30 text-white font-medium px-4 py-2 rounded-lg transition duration-150 ease-in-out text-sm flex items-center space-x-2"
          title="获取一个随机成语"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.828 14.828a4 4 0 01-5.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          <span>随机成语</span>
        </button>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  name: 'AppHeader',
  emits: ['request-random-idiom'],
  methods: {
    goToTutorial() {
      window.open('https://github.com/allenxu09/Nutrimatic-CHN/blob/main/tutorial_cn.md', '_blank');
    }
  }
}
</script>