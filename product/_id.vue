<template>
  <div>
    <div class="container">
      <ol class="breadcrumb bg-white">
        <li class="breadcrumb-item">
          <n-link :to="`/category/${product.category._id}`"><span>{{ product.category.name }}</span></n-link>
        </li>
        <li class="breadcrumb-item"><span>{{ product.title }}</span></li>
      </ol>
    </div>
    <div class="container mt-2">
      <div class="row">
        <div class="col"><img :src="product.image" width="500px"/></div>
        <div class="col">
          <h1 class="text-center">{{ product.title }}</h1>
          <div class="row">
            <div class="col m-auto">
              <h3 class="text-center text-primary">{{ product.brand.name }}</h3>
            </div>
          </div>
          <hr/>

          <div class="row text-right">
            <div class="col">
              <h1>${{ product.price }}</h1>
            </div>
            <div class="col-4">
              <button class="btn btn-primary btn-lg text-right" type="button" @click="addProductToCart(product)">Add
                Cart
                <font-awesome-icon :icon="['fas','cart-plus']"/>
              </button>
            </div>

          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <b-tabs content-class="mt-3">
        <b-tab title="Description" active><p>{{ product.description }}</p></b-tab>
        <b-tab title="Reviews">
          <div class="col">
            <div class="row" v-if="enterReview===true">
              <div class="col m-3">
                <div class="form-group mb-2">
                  <b-form-rating style="width: min-content;color: var(--yellow)" v-model="rating"
                                 show-value></b-form-rating>
                </div>

                <div class="form-group text-right"><textarea class="form-control" v-model="comment"></textarea>
                  <button class="btn btn-primary text-right mt-1" type="button" @click="addReview">Submit
                  </button>
                </div>

              </div>
            </div>
            <div class="row mt-2">
              <div class="col" style="text-align: right;">
                <button class="btn btn-primary text-right mt-1" type="button" @click="enterReview=true">Write Review
                  <font-awesome-icon :icon="['fas','edit']"/>
                </button>
              </div>
            </div>
            <div class="row" v-for="review in reviews" :key="review._id"
                 style="border-bottom-width: 2px;border-bottom-style: solid;">
              <div class="col">
                <div class="card m-2" style="border-style: none;border-bottom-style: none;">
                  <div class="card-body">
                    <font-awesome-icon v-for="index,star in review.rating" :key="index" :icon="['fas','star']"
                                       style="color: var(--yellow)"/>
                    <p class="card-text mt-2">{{ review.comment }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </b-tab>
        <b-tab title="Similar">
          <div class="container">
            <div class="row row-cols-5">
              <div class="col-md-3" v-if="products!==null" v-for="product in products" :key="product._id">
                <n-link :to="`/product/${product._id}`">
                  <img :src="product.image" width="100px">
                </n-link>
              </div>
            </div>
          </div>
        </b-tab>
      </b-tabs>
    </div>
    <div>
    </div>
  </div>
</template>

<script>
import {VueperSlides, VueperSlide} from 'vueperslides'
import 'vueperslides/dist/vueperslides.css'
import {mapActions} from "vuex";

export default {
  data() {
    return {
      rating: 0,
      comment: "",
      enterReview: false,
    }
  },

  async asyncData({$axios, params}) {
    try {
      let product = await $axios.$get(`http://localhost:3000/api/products/${params.id}`);
      let reviews = await $axios.$get(`http://localhost:3000/api/review/${params.id}`);
      let products = await $axios.$get("http://localhost:3000/api/products");
      let id=params.id

      //let products = await $axios.$post(`http://localhost:4000/apriori`, {product:params.id})

      const [productResponse,reviewResponse,productsResponse] = await Promise.all([
        product,
        reviews,
        products
      ])
      return {
        product: productResponse.product,
        reviews: reviewResponse.reviews,
        products: productsResponse.products,
      };


    } catch (err) {
      console.log(err);
    }
  },
  methods: {
    async addReview() {
      try {

        let data = {
          comment: this.comment,
          rating: this.rating,
        }

        let response = await this.$axios.$post(`http://localhost:3000/api/review/${this.$route.params.id}`, data)

        if (response.success) {
          this.enterReview = false
          await this.$nuxt.refresh()
        }
      } catch (err) {
        console.log(err)
      }
    },
    ...mapActions(["addProductToCart"])
  }
}
</script>

<style>

</style>
