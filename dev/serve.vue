
<template>
  <div id="app" class="h-screen" style="background: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAgAAAAICAYAAADED76LAAAAJElEQVQYV2NctWrVfwYkEBYWxojMZ6SDAmT7QGx0K1EcRBsFAADeG/3M/HteAAAAAElFTkSuQmCC') repeat">
    <infinite-scroll-problem />
  </div>
</template>

<script>
import Vue from 'vue'
import InfiniteScrollProblem from '@/src/infinite-scroll-problem.vue'

export default Vue.extend({
  name: 'ServeDev',
  components: {
    InfiniteScrollProblem
  }
})
</script>
