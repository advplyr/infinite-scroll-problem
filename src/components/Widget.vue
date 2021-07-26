<template>
  <div :class="index % 2 === 0 ? 'bg-gray-800' : 'bg-gray-900'" :style="{ height: height + 'px' }" @mouseover="mouseover" @mouseleave="mouseleave">
    <div class="h-full relative pointer-events-none" :style="{ width: width + 'px' }">
      <img :src="src" class="object-cover w-full h-full" />
      <div v-show="isHovering" class="absolute top-0 left-0 z-10 w-full h-full bg-black bg-opacity-75 flex items-center justify-center">
        <p class="text-xl">{{ id }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    id: Number,
    index: Number,
    src: String,
    height: Number,
    width: Number
  },
  data() {
    return {
      isHovering: false
    }
  },
  computed: {},
  methods: {
    mouseover() {
      this.isHovering = true
    },
    mouseleave() {
      this.isHovering = false
    }
  },
  mounted() {}
}
</script>