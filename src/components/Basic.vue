<template>
  <div ref="page" class="w-full h-full">
    <div class="w-full justify-center flex flex-wrap">
      <template v-for="(widget, index) in widgets">
        <widget :key="widget.id" :height="widgetHeight" :width="widgetWidth" :index="index" :id="widget.id" :src="widget.src" />
      </template>
    </div>
    <div class="w-full h-1 relative bg-red-50 block mt-10">
      <div id="observeme" class="absolute -top-20 h-px w-full left-0" />
    </div>
    <div class="fixed top-3 right-3 p-4 bg-gray-600">{{ widgets.length }} of {{ maxWidgets }} Widgets Seen</div>
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
      rowsPerPage: 0,
      maxWidgets: 5000
    }
  },
  computed: {
    widgetsPerPage() {
      return Math.ceil(this.widgetsPerRow * this.rowsPerPage * 1.25)
    }
  },
  methods: {
    loadWidgets(num = 250) {
      var num_to_make = num + this.widgets.length > this.maxWidgets ? this.maxWidgets - this.widgets.length : num
      if (!num_to_make) return

      var _widgets = []
      var startIndex = this.widgets.length
      this.loadingIndex = startIndex
      for (let i = 0; i < num_to_make; i++) {
        var index = startIndex + (i + 1)
        _widgets.push({
          id: index,
          src: 'https://picsum.photos/200/300?random=' + index
        })
      }
      this.widgets = this.widgets.concat(_widgets)
    },
    setObserver() {
      const observer = new IntersectionObserver(
        ([entry]) => {
          if (entry.isIntersecting) {
            if (this.widgets.length >= this.maxWidgets) return
            if (this.loadingIndex === this.widgets.length) {
              console.log('Already loading index', this.loadingIndex)
            } else {
              this.loadWidgets(this.widgetsPerPage)
            }
            return
          }
        },
        {
          threshold: 0.0
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