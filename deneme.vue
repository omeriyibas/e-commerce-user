<template>
  <div>
    <div>
      <vueper-slides
        class="no-shadow"
        :visible-slides="3"
        :slide-ratio="1 / 1"
        :gap="10"
        :touchable="false" fade fixed-height="200px">
        <vueper-slide :link="`/product/${product._id}`" v-for="product in products" :key="product._id" :image="product.image"></vueper-slide>
      </vueper-slides>
    </div>
  </div>

</template>

<script>
import { VueperSlides, VueperSlide } from 'vueperslides'
import 'vueperslides/dist/vueperslides.css'

import Signup from "~/pages/signup";
export default {
  async asyncData({$axios}) {
    try {
      let response = await $axios.$get("http://localhost:3000/api/products");
      return {
        products: response.products
      };
    } catch (err) {
      console.log(err)
    }
  },
  components: {VueperSlides, VueperSlide },
}
</script>

<style scoped>

</style>
