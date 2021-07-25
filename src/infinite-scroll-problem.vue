<template>
  <div class="w-full h-screen">
    <h1>Cat</h1>
    <div class="wrapper w-full"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      widgets: []
    }
  },
  computed: {},
  methods: {
    init() {
      var _widgets = []
      for (let i = 0; i < 1000; i++) {
        _widgets.push({
          id: i + 1,
          src: 'https://picsum.photos/200/300?random=' + (i + 1)
        })
      }
      this.widgets = _widgets
    }
  },
  mounted() {
    this.init()
  }
}
</script>

<style>
.wrapper {
  height: calc(100vh - 40px);
}
</style>