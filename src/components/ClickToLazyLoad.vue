<template>
  <div>
    <button @click="load">Lazy Load</button>
    <component v-if="show" :is="lazyLoad" />
  </div>
</template>

<script>
  export default {
    data() {
      return {
        show: false,
        lazyLoad: null
      }
    },
    methods: {
      load() {
        import('@/LazyLoad.vue').then(mod => {
          console.log(mod.default)
          this.lazyLoad = mod.default
          this.show = true
        })
      }
    }
  }
</script>
