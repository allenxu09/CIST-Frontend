<template>
  <div v-if="message" class="bg-red-100 border-l-4 border-red-500 text-red-700 p-4 mb-6 rounded shadow-md transition-opacity duration-300 ease-in-out" role="alert">
    <p class="font-medium">错误</p>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  name: 'ErrorMessage',
  props: {
    message: {
      type: String,
      default: ''
    }
  }
}
</script>