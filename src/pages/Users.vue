<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">
        <!-- table -->
        <table>
          <!-- head -->
          <thead>
            <tr>
              <th @click="sort('name')">Name &#8595; &#8593;</th>
              <th @click="sort('age')">Age &#8595; &#8593;</th>
              <th @click="sort('gender')">Gender &#8595; &#8593;</th>
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

        <p style="text-align: center">
          <b>sort : {{ currentSort }}, direction: {{ currentSortDir }}</b>
        </p>
      </div>
    </section>

    <section>
      <div class="container">
        <div class="button-list">
          <div class="btn btnPurple" @click="prevPage">&#8592;</div>
          <span>
            <b>shown pages: {{ this.page.current }} from
              {{ totalPages(this.users.length) }}</b>
          </span>
          <div class="btn btnSuccess" @click="nextPage">&#8594;</div>
        </div>
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
      page: {
        //хранит в себе текущую страницу и длину пользователей
        current: 1,
        length: 3, //т.е. будут оторажаться у пользователя на каждой странице
      },
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
    // сортировка
    usersSort() {
      return this.users
        .sort((a, b) => {
          let mod = 1;
          if (this.currentSortDir === "desc") mod = -1;
          if (a[this.currentSort] < b[this.currentSort]) return -1 * mod;
          if (a[this.currentSort] > b[this.currentSort]) return 1 * mod;
          return 0;
        })
        .filter((row, index) => {
          //разрешаем пагинацию
          let start = (this.page.current - 1) * this.page.length;
          let end = this.page.current * this.page.length;
          if (index >= start && index < end) return true;
        });
    },
  },
  methods: {
    // сортировка
    sort(e) {
      if (e === this.currentSort) {
        this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
      }
      this.currentSort = e;
    },

    // Pagination
    prevPage() {
      if (this.page.current > 1) {
        this.page.current -= 1;
      }
    },

    nextPage() {
      if ((this.page.current * this.page.length) < this.users.length) {
        this.page.current += 1;
      }
    },

    totalPages(notes) {
      let result = Math.ceil(notes / this.page.length);
      notes = result;
      return result;
    },
  },
};
</script>

<style lang="scss" scoped>
img {
  width: 50px;
  height: auto;
  border-radius: 50%;
  margin-right: 16px;
}

.button-list {
  text-align: end;

  .btn {
    margin: 0 20px;
  }
}
</style>