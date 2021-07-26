<template>
  <div id="app" class="w-screen h-screen bg-gray-700 text-white overflow-hidden">
    <div class="w-full h-20 flex items-center px-4 border-b border-gray-800 bg-gray-900">
      <h1 class="text-4xl">Infinite Scroll Problem</h1>
      <div class="px-5 py-1 ml-4 text-lg hover:text-opacity-100 cursor-pointer" :class="name === 'basic' ? 'bg-green-600 text-white' : 'bg-green-900 text-white text-opacity-50'" @click="name = 'basic'">Basic</div>
      <div class="px-5 py-1 text-lg hover:text-opacity-100 cursor-pointer" :class="name === 'advanced' ? 'bg-green-600 text-white' : 'bg-green-900 text-white text-opacity-50'" @click="name = 'advanced'">Advanced</div>
    </div>
    <div id="scrollwrapper" class="page w-full relative border-2 overflow-auto mt-0">
      <component :is="name" />
    </div>
  </div>
</template>

<script>
import Basic from './components/Basic.vue'
import Advanced from './components/Advanced.vue'

export default {
  name: 'App',
  components: {
    Basic,
    Advanced
  },
  data() {
    return {
      name: 'basic'
    }
  }
}
</script>

<style>
.page {
  height: calc(100vh - 80px);
  max-height: calc(100vh - 80px);
}
</style>
