<template>
  <div id="app" data-app>
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App',
  created() {
    this.$vuetify.lang.locales = {
      en: {
        dataIterator: {
          noResultsText: "Không có kênh nào",
        },
        dataTable: {
          itemsPerPageText: "Số hàng: ",
          ariaLabel: {
            sortNone: "",
            activateAscending: "Tăng dần",
            sortAscending: "Tăng dần",
            activateDescending: "Giảm dần",
            sortDescending: "Giảm dần",
            activateNone: "",
          },
          sortBy: "Sắp xếp theo",
        },
        noDataText: "Không có kênh nào",
        dataFooter: {
          pageText: "{0}-{1} trong {2}",
          itemsPerPageAll: "Tất cả",
          prevPage: "Trước",
          nextPage: "Tiếp",
        },
      },
    };
  },
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body::-webkit-scrollbar {
    display: none;
}
</style>
