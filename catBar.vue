<template>
  <div>
    <div class="row justify-content-center align-items-center">
      <div class="col">
        <nav class="navbar navbar-light navbar-expand-md bg-light border-primary border rounded-0 shadow-lg">
          <div class="container-fluid">
            <div class="collapse navbar-collapse" id="navcol-1">
              <ul class="navbar-nav border-secondary m-auto" v-for="category in categories" :key="category._id">
                <li class="nav-item"><n-link :to="`/category/${category._id}`">{{ category.name }}</n-link></li>
              </ul>
            </div>
          </div>
        </nav>
      </div>
    </div>


  </div>

</template>

<script>
export default {
  async asyncData({$axios}) {
    try {
      let response = await $axios.$get("http://localhost:3000/api/categories");
      return {
        categories: response.categories
      };
    } catch (err) {
      console.log(err);
    }
  },
}
</script>

<style scoped>

</style>
