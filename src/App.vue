<template>

    <Graph :data="tempData" />
</template>

<script>
import Graph from './components/Graph.vue'

export default {
  name: 'App',
  components: {
    Graph
  },
  data(){
    return{
       tempData: [
        { date: "19-Sep-18", series1: 31, series2: 11 },
        { date: "20-Sep-18", series1: 40, series2: 32 },
        { date: "21-Sep-18", series1: 28, series2: 45 },
        { date: "22-Sep-18", series1: 51, series2: 32 },
        { date: "23-Sep-18", series1: 42, series2: 34 },
        { date: "24-Sep-18", series1: 109, series2: 52 },
        { date: "25-Sep-18", series1: 100, series2: 41 },
      ],
    }
  }
}
</script>

<style>

</style>
