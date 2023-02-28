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

    <div class="row mt-2">
      <div class="col offset-8 text-right mr-auto">
        <button class="btn btn-primary" v-if="desc===true" type="button" @click="sortByDesc">
          <font-awesome-icon :icon="['fas','sort-down']"/>
          Desc
        </button>
      </div>
      <div class="col">
        <button class="btn btn-primary" type="button" v-if="asc===true" @click="sortByAsc">
          <font-awesome-icon :icon="['fas','sort-up']"/>
          Asc
        </button>
      </div>
    </div>
    <div class="row mt-2">
      <div class="col-2 mx-auto">
        <div class="row row-cols-1 mt-5">
          <div class="col" v-for="brand in filterBrand" :key="brand._id">
            <form>
              <div class="form-group text-center"><input type="checkbox" @click="selectOp(brand._id)"/>
                {{ brand.name }}
              </div>
            </form>
          </div>
        </div>
        <hr>
        <div>
          <div class="row-7 mt-5">
            <div class="col">
              <div class="row">
                <div class="col">
                  <h4 class="text-center" v-show="lowPrice>0">Beetween {{ lowPrice }} - {{ highPrice }}</h4>
                </div>
              </div>
              <div class="row row-cols-2">
                <div class="col">
                  <div class="form-group text-center">
                    <form><input type="text" class="form-control" v-model="templowPrice"/><label
                      class="text-center">Min</label></form>
                  </div>
                </div>
                <div class="col">
                  <div class="form-group text-center">
                    <form class="text-center"><input type="text" class="form-control" v-model="temphighPrice"/></form>
                    <label class="text-center">Max</label>
                  </div>
                </div>
              </div>
              <div class="row row-cols-2 text-center">
                <div class="col text-center">
                  <button class="btn btn-primary bg-danger" type="button" @click="clearPrice">Clear</button>
                </div>
                <div class="col text-center">
                  <button class="btn btn-primary bg-success" type="button"
                          @click="rangePrice(templowPrice,temphighPrice)">Filter
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <hr class="verical">


      <div class="col">
        <div class="row row-cols-1">
          <div class="col" v-for="product in filterProducts" :key="product._id">

              <div class="container mt-2">
                <div class="card" id="product-card">
                  <div class="card-body">
                    <div class="row">
                      <div class="col-2"><img :src="product.image" width="100px"/></div>
                      <div class="col">
                        <n-link :to="`/product/${product._id}`"><h4>{{ product.title }}</h4></n-link>
                        <h6 class="text-muted mb-2"><img :src="product.brand.image" width="40px">
                          {{ product.brand.name }}
                        </h6>
                        <font-awesome-icon v-for="star in product.avarageRating" :key="star"  :icon="['fas','star']" style="color: var(--yellow)"/>
                          <div class="row">
                            <div class="col text-right">
                              <h3 class="text-right">${{ product.price }}</h3>
                            </div>
                            <div class="col-2 text-left">
                              <button class="btn btn-primary text-left" type="button" @click="addProductToCart(product)">
                                <font-awesome-icon :icon="['fas','cart-plus']"/>
                              </button>
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
  </div>
</template>

<script>
import { mapActions } from "vuex";
export default {
  created() {
    this.selected.push(this.category._id)
  },
  data() {
    return {
      selected: [],
      selection: "",
      asc: true,
      desc: false,
      status: false,
      highPrice: null,
      lowPrice: null,
      temphighPrice: null,
      templowPrice: null,
    }
  },
  async asyncData({$axios, params}) {
    try {
      let brands = $axios.$get("http://localhost:3000/api/brands");
      let products = $axios.$get("http://localhost:3000/api/products")
      let category = $axios.$get(`http://localhost:3000/api/categories/${params.id}`)
      let categories = $axios.$get(`http://localhost:3000/api/categories/`)


      const [catsResponse, catResponse, brandResponse, productResponse] = await Promise.all([
        categories,
        category,
        brands,
        products,
      ])
      return {
        categories: catsResponse.categories,
        category: catResponse.category,
        brands: brandResponse.brands,
        products: productResponse.products
      }
    } catch (err) {
      console.log(err)
    }
  },
  methods: {
    ...mapActions(["addProductToCart"]),
    rangePrice(a, b) {
      this.lowPrice = a
      this.highPrice = b
    },
    clearPrice() {
      this.lowPrice = null
      this.highPrice = null
    },
    selectOp(selection) {
      if (this.status === false) {
        this.selected.pop()
      }
      let flag = this.selected.includes(selection)

      if (flag) {
        this.selected.splice(this.selected.indexOf(selection), 1)
        this.status = true
      }
      if (!flag) {
        this.selected.push(selection)
        this.status = true
      }
      if (this.selected.length === 0) {
        this.selected.push(this.category._id)
        this.status = false
      }
    },
    sortPrice: function (a, b) {
      if (a.price < b.price) {
        return -1 * this.modifier
      }
      if (a.price > b.price) {
        return 1 * this.modifier
      }
      return 0
    },
    sortByAsc: function () {
      this.asc = false
      this.desc = true
      this.modifier = 1
      console.log(this.asc)
      return this.products.sort(this.sortPrice)
    },
    sortByDesc: function () {
      this.asc = true
      this.desc = false
      console.log(this.asc)
      this.modifier = -1
      return this.products.sort(this.sortPrice)
    }
  },
  computed: {
    filterProducts: function () {
      if (this.selected.length === 0) {
        return this.products
      } else {
        return this.products.filter(product => {
            return this.selected.some(selection => {
              if (product.brand._id.includes(selection)) {
                if (this.lowPrice != null && this.lowPrice != null) {
                  return product.brand._id.includes(selection) && product.price > this.lowPrice && product.price < this.highPrice
                }
                return product.brand._id.includes(selection)

              }
              if (product.category._id.includes(selection)) {
                if (this.lowPrice != null && this.lowPrice != null) {
                  return product.category._id.includes(selection) && product.price > this.lowPrice && product.price < this.highPrice
                }
                return product.category._id.includes(selection)
              }
            })
          }
        )
      }
    },
    filterBrand: function () {
      let cat = this.category._id
      return this.brands.filter(function (brand) {
        return brand.category._id === cat
      })
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
