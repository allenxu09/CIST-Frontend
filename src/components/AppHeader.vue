<template>
  <nav class="bg-gradient-to-r from-blue-700 to-blue-800 text-white shadow-lg sticky top-0 z-40">
    <div class="container mx-auto px-4 py-3 flex items-center justify-between max-w-5xl">
      <h1 class="text-2xl font-bold tracking-tight">成语查询</h1>
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
  </nav>
</template>

<script>
export default {
  name: 'AppHeader',
  emits: ['request-random-idiom']
}
</script>