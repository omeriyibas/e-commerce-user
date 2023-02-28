<template>
  <div>
    <div class="row justify-content-center align-items-center">
      <div class="col">
        <nav class="navbar navbar-light navbar-expand-md bg-light border-primary border rounded-0 shadow-lg">
          <div class="container-fluid">
            <div class="collapse navbar-collapse" id="navcol-1">
              <ul class="navbar-nav border-secondary m-auto" v-for="category in categories" :key="category._id">
                <li class="nav-item">
                  <n-link :to="`/category/${category._id}`">{{ category.name }}</n-link>
                </li>
              </ul>
            </div>
          </div>
        </nav>
      </div>
    </div>
    <div class="col">
      <div class="row row-cols-1">
        <div class="col" v-for="product in products" :key="product._id">
          <n-link :to="`/product/${product._id}`">
            <div class="container mt-2">
              <div class="card" id="product-card">
                <div class="card-body">
                  <div class="row">
                    <div class="col-2"><img :src="product.image" width="100px"/></div>
                    <div class="col">
                      <h4>{{ product.title }}</h4>
                      <h6 class="text-muted mb-2"><img :src="product.brand.image" width="40px">
                        {{ product.brand.name }}
                      </h6>
                      <font-awesome-icon v-for="star in product.avarageRating" :key="star" :icon="['fas','star']"
                                         style="color: var(--yellow)"/>

                      <div class="row">
                        <div class="col text-right">
                          <h3 class="text-right">${{ product.price }}</h3>
                        </div>
                        <div class="col-2 text-left">
                          <button class="btn btn-primary text-left" type="button">
                            <font-awesome-icon :icon="['fas','cart-plus']"/>
                          </button>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </n-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  watch: {
    '$route.query'() {
      this.$nuxt.refresh()
    },
  },
  async asyncData({$axios,query}) {
    try {
      console.log(query.title)

      let products = await $axios.$post('http://localhost:3000/api/search',{title:query.title})
      let categories = await $axios.$get(`http://localhost:3000/api/categories/`)

      const [catsResponse, productResponse] = await Promise.all([
        categories,
        products,
      ])
      return {
        categories: catsResponse.categories,
        products: productResponse
      }

    return{
      products:response
    }
    } catch (err) {
      console.log(err)
    }
  },
}

</script>

<style>
.verical {

  border: none;
  border-left: 1px solid hsla(200, 10%, 50%, 100);
  height: 100vh;
  width: 1px;
}

#product-card:hover {
  transform: scale(1.1);
}
</style>
