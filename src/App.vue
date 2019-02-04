<template>
  <div id="app">
    <grid-table :columns="columns" :dataItems="dataItems" :max-items="10" />
  </div>
</template>

<script>
import GridTable from './components/GridTable.vue';
import data from './data/initialData.js';

export default {
  name: 'app',
  data: function() {
    return {
      columns: getColumns(data.titles),
      dataItems: data.items
    };
  },
  components: {
    GridTable
  }
};

function getColumns(data) {
  let columns = [];
  for (const key in data) {
    if (data.hasOwnProperty(key)) {
      columns.push({
        key,
        title: data[key]
      });
    }
  }
  return columns;
}
</script>

<style></style>
