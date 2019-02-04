<template>
  <div>
    <table
      class="table table-dark table-striped table-bordered table-hover table-sm table-responsive"
    >
      <thead class="thead-light">
        <tr>
          <th v-for="column in columns" :key="column.key">
            <div @click="sortByColumn(column.key)">
              {{ column.title }}
              <span class="fa fa-fw fa-sort"> </span>
            </div>
            <filter-input :title="column.title" v-model="filters[column.key]"/>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(entry, index) in paginatedData" :key="index">
          <td
            v-for="column in columns"
            :key="column.key"
            contenteditable="true"
            @keydown.enter.exact.prevent
            @keyup.enter.exact="updateItem(entry, column.key, $event)"
          >
            {{ entry[column.key] }}
          </td>
        </tr>
      </tbody>
    </table>
    <basic-pagination
      :page-count="pagesCount"
      :selected-page="selectedPage"
      :on-click-handler="onPageChange"
    />
  </div>
</template>

<script>
import BasicPagination from './BasicPagination.vue'
import FilterInput from './FilterInput.vue'

export default {
  name: 'GridTable',
  components: {
    BasicPagination,
    FilterInput
  },
  props: {
    columns: Array,
    dataItems: Array,
    maxItems: Number
  },
  data: function () {
    var sortOrders = {};
    var filters = {};

    this.columns.forEach(function (column) {
      let key = column.key;
      sortOrders[key] = 1;
      filters[key] = '';
    });

    return {
      sortKey: '',
      sortOrders,
      filters,
      pageNum: 1
    };
  },
  computed: {
    filteredData: function () {
      var sortKey = this.sortKey;
      var order = this.sortOrders[sortKey] || 1;
      var data = this.dataItems;

      for (const key in this.filters) {
        if (this.filters.hasOwnProperty(key)) {
          const filterKey = this.filters[key] && this.filters[key].toLowerCase();
          if (filterKey) {
            data = data.filter(
              item =>
                String(item[key])
                  .toLowerCase()
                  .indexOf(filterKey) > -1
            );
          }
        }
      }

      if (sortKey) {
        data = data.slice().sort(function (a, b) {
          a = a[sortKey].toLowerCase();
          b = b[sortKey].toLowerCase();
          return order * a.localeCompare(b, undefined, {numeric: true, sensitivity: 'base'});
        })
      }
      return data;
    },
    paginatedData: function () {
      var data = this.filteredData;
      let num = this.selectedPage - 1;
      return data.slice(num * this.maxItems, (num + 1) * this.maxItems);
    },
    pagesCount: function () {
      return Math.ceil(this.filteredData.length / this.maxItems);
    },
    selectedPage: function () {
      return this.pageNum > this.pagesCount ? 1 : this.pageNum;
    }
  },
  methods: {
    sortByColumn: function(key) {
      this.sortKey = key;
      this.sortOrders[key] = this.sortOrders[key] * -1;
    },
    onPageChange: function (pageNum) {
      this.pageNum = pageNum;
    },
    updateItem: function (item, key, event) {
      event.currentTarget.blur();
      if (item) {
        item[key] = event.target.innerText;
      }
    }
  }
};
</script>

<style scoped>
</style>
