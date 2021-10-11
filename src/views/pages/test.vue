<template>
  <div class="main">
       {{ title }}
       {{ name }}
    <input v-model="name"/>
    <button @click="viewName()">view</button>
  </div>
</template>
<script>
export default {
  data: () => ({
    title: 'YOO',
    name: 'YOO',
    array: []
  }),
  methods: {
    viewName() {
      alert(this.name)
    },
    fncName() {
      const _idx1 = 0;
      this.name = 'YOO1'
    },
    fncName2() {
      const _idx2 = 0;
      this.name = 'YOO2'
    }
  },
  mounted() {
    this.fncName2()
  }
}
</script>
<style>
.main {
  width: 100%;
}
</style>