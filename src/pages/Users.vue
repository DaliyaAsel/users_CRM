<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">
        <!-- table -->
        <table>
          <!-- head -->
          <thead>
            <tr>
              <th @click="sort('name')">Name</th>
              <th @click="sort('age')">Age</th>
              <th @click="sort('gender')">Gender</th>
            </tr>
          </thead>

          <!-- body -->
          <tbody>
            <tr v-for="user in usersSort" :key="user.id">
              <td>
                <img :src="user.img" :alt="user.name" />
                <span> {{ user.name }} </span>
              </td>
              <td>{{ user.age }}</td>
              <td>{{ user.gender }}</td>
            </tr>
          </tbody>
        </table>
        <p>debag sort : {{ currentSort }}, dir: {{ currentSortDir }}</p>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      users: [],
      currentSort: "name",
      currentSortDir: "asc",
    };
  },
  created() {
    axios
      .get("http://localhost:3000/users")
      .then((response) => {
        //  console.log(response.data)
        this.users = response.data;
      })
      .catch((error) => {
        // this.$emit('error')
        console.log(error);
      });
  },
  computed: {
    usersSort() {
      return this.users.sort((a, b) => {
        let mod = 1;
        if (this.currentSortDir === "desc") mod = -1;
        if (a[this.currentSort] < b[this.currentSort]) return -1 * mod;
        if (a[this.currentSort] > b[this.currentSort]) return 1 * mod;
        return 0;
      });
    },
  },
  methods: {
    sort(e) {
      if (e === this.currentSort) {
        this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
      }
      this.currentSort = e;
    },
  },
};
</script>

<style scoped>
img {
  width: 50px;
  height: auto;
  border-radius: 50%;
  margin-right: 16px;
}
</style>