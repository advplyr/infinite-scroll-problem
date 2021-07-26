<template>
  <div ref="page" class="w-full h-full relative bg-red-50">
    <div class="bg-red-500 bg-opacity-50" :style="{ height: totalHeight + 'px' }" />

    <div class="w-full justify-center flex flex-wrap absolute bottom-0 left-0 right-0 bg-gray-700" :style="{ top: widgetWindowTop + 'px', height: rowsPerPage * widgetHeight + 'px' }">
      <template v-for="(widget, index) in widgetsVisible">
        <widget :key="widget.id" :height="widgetHeight" :width="widgetWidth" :index="index" :id="widget.id" :src="widget.src" />
      </template>
    </div>

    <div class="fixed top-3 right-3 py-1 text-xs bg-gray-600">
      <div class="flex">
        <div class="w-64 px-2 border-r border-gray-500">
          <p class="font-semibold font-sans">Window: {{ viewportTop.toFixed(0) }}px - {{ viewportBottom.toFixed(0) }}px</p>
          <p class="text-sm">Showing Widgets {{ startIndex }} to {{ startIndex + widgetsPerPage }}</p>
        </div>
        <div class="w-64 px-2 border-r border-gray-500">
          <p>{{ widgetsPerRow }} per row</p>
          <p>{{ rowsPerPage }} rows per page | {{ Math.ceil(widgetsVisible.length / widgetsPerRow) }} Visible</p>
          <p>{{ widgetsPerPage }} per page | {{ widgetsVisible.length }} Visible</p>
        </div>
        <div class="w-64 px-2">
          <p class="text-lg">{{ widgetsSeen }} of {{ maxWidgets }} Widgets Seen</p>
          <!-- <p>{{ widgetHeight }}x{{ widgetWidth }} pixels</p> -->
        </div>
      </div>
    </div>
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
      maxWidgets: 5000,
      widgetsSeen: 0,

      startRow: 0,
      startIndex: 0,
      totalRows: 0,
      totalHeight: 0,

      viewportHeight: 0,
      viewportWidth: 0,
      widgetHeight: 60,
      widgetWidth: 60,
      widgetsPerRow: 0,
      rowsPerPage: 0,
      widgets: [],
      scrollwrapper: null,
      viewportTop: 0,
      viewportBottom: 0,

      widgetWindowTop: 0
    }
  },
  computed: {
    widgetsVisible() {
      return this.widgets.slice(this.startIndex, this.startIndex + this.widgetsPerPage)
    },
    widgetsPerPage() {
      return this.widgetsPerRow * this.rowsPerPage
    },
    widgetViewportHeight() {
      return this.rowsPerPage * this.widgetHeight
    },
    widgetWindowBottom() {
      return this.widgetWindowTop + this.widgetViewportHeight
    }
  },
  methods: {
    loadWidgets(num = 5000) {
      var _widgets = []
      var startIndex = this.widgets.length
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
    init() {
      if (!this.widgets.length) {
        this.loadWidgets(this.maxWidgets)
      }

      this.viewportWidth = this.$refs.page.clientWidth
      this.viewportHeight = this.$refs.page.clientHeight
      this.widgetsPerRow = Math.floor(this.viewportWidth / this.widgetWidth)
      this.rowsPerPage = Math.ceil(this.viewportHeight / this.widgetHeight) + 2

      this.totalRows = Math.ceil(this.widgets.length / this.widgetsPerRow)
      this.totalHeight = this.totalRows * this.widgetHeight
      this.$nextTick(this.setWidgetsVisible)
    },
    setWidgetsVisible() {
      var startRow = Math.floor(this.viewportTop / this.widgetHeight)
      this.startRow = Math.max(0, startRow - 1)

      var sindex = this.startRow * this.widgetsPerRow
      var min = sindex + this.widgetsPerPage
      if (min > this.widgets.length) {
        this.startRow--
      }

      this.startIndex = sindex

      if (this.startIndex + this.widgetsPerPage > this.widgetsSeen) {
        this.widgetsSeen = this.startIndex + this.widgetsPerPage
      }
    },
    scroll() {
      var st = this.scrollwrapper.scrollTop
      var ch = this.scrollwrapper.clientHeight

      this.viewportTop = st
      this.viewportBottom = st + ch

      var diffFromBottom = this.widgetWindowBottom - this.viewportBottom
      var diffFromTop = this.viewportTop - this.widgetWindowTop
      if (diffFromBottom < this.widgetHeight) {
        this.widgetWindowTop = Math.min(this.totalHeight - this.widgetViewportHeight, Math.max(0, this.viewportTop - this.widgetHeight))
        this.setWidgetsVisible()
      } else if (diffFromTop < this.widgetHeight) {
        this.widgetWindowTop = Math.max(0, this.viewportTop - this.widgetHeight)
        this.setWidgetsVisible()
      }
    },
    resize() {
      console.log('Window resized')
      this.init()
    }
  },
  mounted() {
    this.scrollwrapper = document.getElementById('scrollwrapper')
    this.scrollwrapper.addEventListener('scroll', this.scroll)
    window.addEventListener('resize', this.resize)

    this.init()
  },
  beforeDestroy() {
    if (this.scrollwrapper) {
      this.scrollwrapper.removeEventListener('scroll', this.scroll)
    }
    window.removeEventListener('resize', this.resize)
  }
}
</script>
