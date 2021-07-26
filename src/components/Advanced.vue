<template>
  <div ref="page" class="w-full h-full relative bg-red-50">
    <div class="bg-red-500 bg-opacity-50" :style="{ height: totalHeight + 'px' }" />

    <div class="w-full justify-center flex flex-wrap absolute bottom-0 left-0 right-0 bg-gray-700" :style="{ top: widgetWindowTop + 'px', height: widgetViewportHeight + 'px' }">
      <template v-for="(widget, index) in widgetsVisible">
        <widget :key="widget.id" :height="widgetHeight" :width="widgetWidth" :index="index" :id="widget.id" :src="widget.src" />
      </template>
      <template v-for="row in rowsPerPage + 1">
        <div :key="row" class="absolute left-2 h-5 px-1 py-0.5 bg-black bg-opacity-75 text-xs" :style="{ top: (row - 1) * widgetHeight + widgetHeight / 2 - 10 + 'px' }">Row {{ row + startRow }}</div>
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
          <p>{{ rowsPerPage }} rows per page | {{ Math.ceil(widgetsVisible.length / widgetsPerRow) }} Visible | {{ totalRows }} Total</p>
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
      maxWidgets: 15000,
      widgetsSeen: 0,

      startRow: 0,
      startIndex: 0,
      totalHeight: 0,
      totalRows: 0,

      viewportHeight: 0,
      viewportWidth: 0,
      widgetHeight: 60,
      widgetWidth: 60,
      widgetsPerRow: 0,
      rowsPerPage: 0,
      bufferRows: 2,
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
      return this.widgetsPerRow * (this.rowsPerPage + this.bufferRows)
    },
    widgetViewportHeight() {
      return (this.rowsPerPage + this.bufferRows) * this.widgetHeight
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
    },
    init() {
      if (!this.widgets.length) {
        this.loadWidgets(this.maxWidgets)
      }

      this.viewportWidth = this.$refs.page.clientWidth
      this.viewportHeight = this.$refs.page.clientHeight
      this.widgetsPerRow = Math.floor(this.viewportWidth / this.widgetWidth)
      this.rowsPerPage = Math.ceil(this.viewportHeight / this.widgetHeight)

      var totalRows = Math.ceil(this.widgets.length / this.widgetsPerRow)
      this.totalHeight = totalRows * this.widgetHeight
      this.totalRows = totalRows
      this.$nextTick(this.setWidgetsVisible)
    },
    setWidgetsVisible() {
      var startRow = Math.floor(this.viewportTop / this.widgetHeight)
      // Start 1 row back as a buffer
      startRow = Math.max(0, startRow - 1)
      this.startRow = startRow

      var startIndex = startRow * this.widgetsPerRow
      var lastIndex = startIndex + this.widgetsPerPage

      if (lastIndex > this.maxWidgets) {
        this.bufferRows = 1
        console.log('Only 1 buffer row', this.widgetViewportHeight)
      } else {
        this.bufferRows = 2
        console.log('Only 2 buffer row', this.widgetViewportHeight)
      }

      this.startIndex = startRow * this.widgetsPerRow

      var lastWidget = Math.max(this.maxWidgets, this.startIndex + this.widgetsPerPage)
      if (lastWidget > this.widgetsSeen) {
        this.widgetsSeen = lastWidget
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
        this.setWidgetsVisible()
        this.widgetWindowTop = Math.min(this.totalHeight - this.widgetViewportHeight, Math.max(0, this.viewportTop - this.widgetHeight))
      } else if (diffFromTop < this.widgetHeight) {
        this.setWidgetsVisible()
        this.widgetWindowTop = Math.max(0, this.viewportTop - this.widgetHeight)
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
