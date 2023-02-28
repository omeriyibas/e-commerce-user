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
    <div>
      <div class="col mt-5">
        <div class="container">
          <vueper-slides :touchable="false" fade fixed-height="500px">
            <vueper-slide :link="`/product/${product._id}`" v-for="product in products" :key="product._id" :image="product.image"></vueper-slide>
          </vueper-slides>
        </div>
      </div>
    </div>

  </div>
</template>
<script>

import { VueperSlides, VueperSlide } from 'vueperslides'
import 'vueperslides/dist/vueperslides.css'
import Signup from "~/pages/signup";
export default {
  data() {
    return {
      loginStatus: "false"
    }
  },
  components: {Signup, VueperSlides, VueperSlide },
  async asyncData({$axios, params}) {
    try {
      let brands = $axios.$get("http://localhost:3000/api/brands");
      let products = $axios.$get("http://localhost:3000/api/products")
      let categories = $axios.$get(`http://localhost:3000/api/categories/`)

      const [catsResponse,brandResponse, productResponse] = await Promise.all([
        categories,
        brands,
        products,
      ])
      return {
        categories: catsResponse.categories,
        brands: brandResponse.brands,
        products: productResponse.products
      }
    } catch (err) {
      console.log(err)
    }
  },

}
</script>

<style>


</style>
