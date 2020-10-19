<template>
  <div id="app">
    <ComplianceTable></ComplianceTable>
  </div>
</template>

<script>
import ComplianceTable from "@/components/ComplianceTable";

import "bootstrap"
import 'bootstrap/dist/css/bootstrap.css'

export default {
  name: 'App',
  components: {
    ComplianceTable
  }
}

</script>

<style>
body {
  background-color: #fbfbfb;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
