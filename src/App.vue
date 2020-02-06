<template>
  <table>
    <thead>
      <tr>
        <th @click="sortBreweries('name')">Name</th>
        <th @click="sortBreweries('city')">City</th>
        <th @click="sortBreweries('state')">State</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="b in breweries" :key="b.id">
        <td>{{ b.name }}</td>
        <td>{{ b.city }}</td>
        <td>{{ b.state }}</td>
      </tr>
    </tbody>
  </table>
  <p>
    <button @click="prevPage" :disabled="cantGoBack">Previous</button>
    <button @click="nextPage">Next</button>
  </p>
</template>

<script>
import { computed, reactive, ref, watch } from "vue";

export default {
  setup() {
    const breweries = ref([]);
    const page = reactive({ current: 1, size: 20 });
    const sort = reactive({ key: "name", dir: "asc" });
    const cantGoBack = computed(() => page.current == 1);
    const sortStr = computed(() =>
      sort.dir === "asc" ? sort.key : "-" + sort.key
    );

    watch(async () => {
      const response = await fetch(
        `https://api.openbrewerydb.org/breweries?page=${page.current}&per_page=${page.size}&sort=${sortStr.value}`
      );
      breweries.value = await response.json();
    });

    function sortBreweries(s) {
      //if s == current sort, reverse
      if (s === sort.key) {
        sort.dir = sort.dir === "asc" ? "desc" : "asc";
      } else {
        sort.dir = "asc";
      }
      sort.key = s;
    }

    function nextPage() {
      page.current++;
    }

    function prevPage() {
      if (page.current > 1) {
        page.current--;
      }
    }

    return { breweries, sortBreweries, prevPage, nextPage, cantGoBack };
  }
};
</script>

<style>
table {
  padding: 0;
  margin: 0;
  border-collapse: collapse;
}
th {
  cursor: pointer;
}
td {
  padding: 4px;
  margin: 0px;
}
tr:nth-child(even) {
  background-color: rgb(238, 238, 238);
}
</style>
