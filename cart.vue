<template>
  <div>
    <div class="row">
      <div class="col text-center mt-2">
        <h1>Shopping Cart</h1>
        <hr>
      </div>
    </div>
  <div class="row">
    <div class="col">
      <div class="row row-cols-1" v-for="product in getCart" :key="product._id">
        <div class="col">
          <div class="container mt-2">
            <div class="card">
              <div class="card-body">
                <div class="row row-cols-3">
                  <div class="col-2"><img :src="product.image" width="100px"/></div>
                  <div class="col-7">
                    <h4>{{ product.title }}</h4>
                    <h6 class="text-muted mb-2"><img :src="product.brand.image" width="40px">  {{ product.brand.name }}</h6>
                    <p>only left {{product.stockQuantity}}</p>
                    <h3 class="text-left">${{product.price*product.quantity}}</h3>
                  </div>
                  <div class="col-2 m-auto">
                    <div class="row row-cols-2">
                      <div class="col text-center m-auto">
                        <div class="card">
                          <input type="text" v-model="product.quantity"/>
                        </div>
                      </div>
                      <div class="col">
                        <div class="row text-center">
                          <div class="col"><button class="btn btn-primary" type="button" @click="increaseQuantity(product)" v-if="product.quantity<=product.stockQuantity-1"><font-awesome-icon :icon="['fas','arrow-up']"/></button></div>
                        </div>
                        <div class="row text-center" v-if="product.quantity!==1">
                          <div class="col mt-3"><button class="btn btn-primary" type="button" @click="decreaseQuantity(product)"><font-awesome-icon :icon="['fas','arrow-down']"/></button></div>
                        </div>
                        <div class="row text-center" v-if="product.quantity===1">
                          <div class="col mt-3"><button class="btn btn-primary" type="button" @click="onDeleteProduct(product)"><font-awesome-icon :icon="['fas','trash-alt']"/></button></div>
                        </div>

                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="col-3 mr-5">
      <div class="card text-center">
        <div class="card-body m-5">
          <h4 class="card-title">Subtotal Price</h4>
          <h6 class="text-muted card-subtitle mb-2">{{ getCartTotalPrice }} $</h6><b-link :to="`/checkout/${this.$auth.$state.user.address}`" class="btn btn-primary">Checkout</b-link>
        </div>
      </div>
    </div>
  </div>
  </div>

</template>

<script>
import {mapGetters} from "vuex"
export default {
  computed:{
    ...mapGetters(["getCart","getCartTotalPrice"])
  },
  methods:{
    async goPage(){
      await this.$router.push("/checkout")
    },
    increaseQuantity(product){
      let quantity=product.quantity+1
      this.$store.commit("changeQuantity",{product,quantity}); //miktarı store üzerinden değiştir
      console.log(product.quantity)
    },
    decreaseQuantity(product){
      let quantity=product.quantity-1
      this.$store.commit("changeQuantity",{product,quantity}); //miktarı store üzerinden değiştir
    },
    onDeleteProduct(product){
      this.$store.commit("removeProduct",product)
    }

  },
  data() {
  },
  name: "cart",
  async asyncData({$axios, params}) {
    try {
      let products = $axios.$get("http://localhost:3000/api/products")


      const [productResponse] = await Promise.all([
        products
      ])
      return {
        products: productResponse.products
      }
    } catch (err) {
      console.log(err)
    }
  }
}
</script>

<style scoped>

</style>
