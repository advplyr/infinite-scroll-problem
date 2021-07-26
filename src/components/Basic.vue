<template>
  <div ref="page" class="w-full h-full">
    <div class="w-full justify-center flex flex-wrap">
      <template v-for="(widget, index) in widgets">
        <widget :key="widget.id" :height="widgetHeight" :width="widgetWidth" :index="index" :id="widget.id" :src="widget.src" />
      </template>
    </div>
    <div id="observeme" class="w-full h-1 bg-red-500 block mt-10" />
    <div class="fixed top-3 right-3 p-4 bg-gray-600">{{ widgets.length }} Widgets</div>
  </div>
</template>

<script>
import Widget from './Widget.vue'

export default {
  components: {
    Widget
  },
  data() {
    return {
      widgets: [],
      loadingIndex: 0,
      widgetHeight: 60,
      widgetWidth: 60,
      widgetsPerRow: 0,
      rowsPerPage: 0
    }
  },
  computed: {
    widgetsPerPage() {
      return this.widgetsPerRow * this.rowsPerPage
    }
  },
  methods: {
    loadWidgets(num = 250) {
      var _widgets = []
      var startIndex = this.widgets.length
      this.loadingIndex = startIndex
      for (let i = 0; i < num; i++) {
        var index = startIndex + (i + 1)
        _widgets.push({
          id: index,
          src: 'https://picsum.photos/200/300?random=' + index
        })
      }
      this.widgets = this.widgets.concat(_widgets)
      console.log('Num Widgets', this.widgets.length)
    },
    setObserver() {
      const observer = new IntersectionObserver(
        ([entry]) => {
          if (entry.isIntersecting) {
            if (this.loadingIndex === this.widgets.length) {
              console.log('Already loading index', this.loadingIndex)
            } else {
              this.loadWidgets(this.widgetsPerPage)
            }
            return
          }
        },
        {
          root: null,
          threshold: 0.1
        }
      )
      observer.observe(document.getElementById('observeme'))
    },
    init() {
      var viewportWidth = this.$refs.page.clientWidth
      var viewportHeight = this.$refs.page.clientHeight
      this.widgetsPerRow = Math.floor(viewportWidth / this.widgetWidth)
      this.rowsPerPage = Math.ceil(viewportHeight / this.widgetHeight)
      this.loadWidgets(this.widgetsPerPage)

      this.setObserver()
    }
  },
  mounted() {
    this.init()
  }
}
</script>

<style>
</style>