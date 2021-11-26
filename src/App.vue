<template>
  <div id="app">
    <TableWrapper />
  </div>
</template>

<script>
import TableWrapper from '@/components'
export default {
  name: 'App',
  components: {
    TableWrapper,
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

  width: 1200px;
  margin: 50px auto;
  position: absolute;
  top: 60px;
  left: 100px;
}
</style>
