<template>
  <Table :tableData="mockData" :pageSize="12"></Table>
</template>
<script>
import Table from '@/components/Table'
import mockData from '@/mock/mock.js'
export default {
  data() {
    return {
      mockData: mockData(),
    }
  },
  components:{
    Table
  }
}
</script>
<style>

</style>

