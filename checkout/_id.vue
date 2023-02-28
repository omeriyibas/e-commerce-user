<template>
<div>
  <div class="row">
    <div class="col-8 mt-2 ml-3">
      <h1>Review Your Order</h1>
      <hr />
      <div class="row" >
        <div class="col" v-if="address==null">
        <div class="container mt-5">
          <div class="col text-left">
            <n-link to="/address/add">
              <font-awesome-icon :icon="['fas','plus']"/>
              address
            </n-link>
          </div>
        </div>
        </div>
        <div class="col" v-if="address!=null">
          <div class="card">
            <div class="card-body">
              <h4 class="card-title">Shipping Adress</h4>
              <h6 class="text-muted card-subtitle mb-2">{{ $auth.$state.user.name }}</h6>
              <h6 class="text-muted card-subtitle mb-2">{{address.fullAddress}}</h6>
              <h6 class="text-muted card-subtitle mb-2">{{address.county.name}}</h6>
              <h6 class="text-muted card-subtitle mb-2">{{address.phoneNumber}}</h6>
              <nuxt-link to="/address" class="card-link text-right" >change Address</nuxt-link>
            </div>
          </div>
        </div>
        <div class="col">
          <div class="card h-100">
            <div class="card-body text-right p-4">
              <h4 class="text-primary card-title">Select Shipping Type</h4>
              <div class="form-check">
                <div class="form-check mt-3">
                  <input type="radio" class="form-check-input" id="formCheck1" name="shipping" value="Standard" v-model="shipType"  @change="onChooseShipping('standard',address.county.shipPrice)" />
                  <label class="form-check-label" for="formCheck1">Standard - 3 Day Delivery</label>
                </div>
                <div class="form-check mt-3">
                  <input type="radio" class="form-check-input" id="formCheck2" name="shipping" value="Express" v-model="shipType" @change="onChooseShipping('express',address.county.shipPrice)" />
                  <label class="form-check-label" for="formCheck2" >Express - 1 Day Delivery<br /></label></div>
              </div>
              <h5 class="mt-3 text-warning">Estimated Delivary Date {{shippingEstimatedDelivery}}</h5>
              </div>
          </div>
        </div>
      </div>
      <div class="row mt-3">
        <div class="col">
          <div class="card ml-2">
            <div class="row row-cols-1 mt-2" v-for="product in getCart" :key="product._id">
              <div class="col-auto ml-5"><img :src="product.image" style="width: 100px;" /></div>
              <div class="col-4">
                <h4>{{ product.title }}</h4>
                <h6 class="text-muted mb-2">{{ product.price }}$</h6>
                <p>Quantity: {{product.quantity}}</p>
                <hr />

              </div>
              <div class="col-4">
                <div class="row text-center">
                  <div class="col mt-3"><button class="btn btn-primary" type="button" @click="onDeleteProduct(product)" ><font-awesome-icon :icon="['fas','trash-alt']"/></button></div>
                </div>
              </div>

            </div>
          </div>
        </div>
        <div class="col">
          <div class="card">
            <div class="card-body text-right p-4">
              <h4 class="text-primary card-title">Select Payment Options</h4>
              <div class="form-check">
                <div class="form-check mt-3"><input type="radio" class="form-check-input" id="formCheck-1" name="pay" value="1" @change="onhireSelected(1)" checked /><label class="form-check-label" for="formCheck-1">Cash</label></div>
                <div class="form-check mt-3"><input type="radio" class="form-check-input" id="formCheck-2" name="pay" value="3" @change="onhireSelected(3)" /><label class="form-check-label" for="formCheck-2">3 Month<br /></label></div>
                <div class="form-check mt-3"><input type="radio" class="form-check-input" id="formCheck-3" name="pay" value="6" @change="onhireSelected(6)" /><label class="form-check-label" for="formCheck-3">6 Month<br /></label></div>
                <div class="form-check mt-3"><input type="radio" class="form-check-input" id="formCheck-4" name="pay" value="9" @change="onhireSelected(9)" /><label class="form-check-label" for="formCheck-4">9 Month<br /></label></div>
                <div class="form-check mt-3"><input type="radio" class="form-check-input" id="formCheck-5" name="pay" value="12" @change="onhireSelected(12)" /><label class="form-check-label" for="formCheck-5">12 Month<br /></label></div>

              </div>

            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="col-3 mt-5 mr-5">
      <div class="card">
        <div class="card-body text-left pl-5"><button class="btn btn-primary btn-block" @click="onBought">Buy Now</button>
          <h6 class="text-muted card-subtitle mb-2 mt-3">Product Cost: {{getCartTotalPrice}}$</h6>
          <h6 class="text-muted card-subtitle mb-2">Shipping Cost: {{shippingPrice}}$</h6>
          <h6 class="text-muted card-subtitle mb-2">Interest Cost: {{getHirePrice}}</h6>
          <hr />
          <h6 class="text-muted card-subtitle mb-2">Total Cost:{{getCartTotalPrice+shippingPrice+getHirePrice}}</h6>
        </div>
      </div>
      <div class="form-group mt-2" v-if="isCredit_Sufficent===false">
        <div class="card text-danger border-primary border rounded-0">
          <p class="m-auto">credit is not sufficent</p>
        </div>
      </div>
    </div>
  </div>

</div>
</template>

<script>
import {mapGetters,mapState} from "vuex"
export default {
  data() {
    return {
      hire: 1,
      shipType:"standard",
      isCredit_Sufficent:true,
    }
  },

  async asyncData({$axios, params,store}) {
    try {
      let address= await $axios.$get(`http://localhost:3000/api/addresses/${params.id}`)
      let shipment=await $axios.$post('http://localhost:3000/api/shipment',{shipment:"standard",shipPrice:0})
      const[addressResponse,shipmentResponse]=await Promise.all([
        address,
        shipment
    ]);
      store.commit("setShipping", {
        price: shipmentResponse.shipment.price,
        estimatedDelivery: shipmentResponse.shipment.estimated
      });

      return {
        address:addressResponse.address,
        shipment:shipmentResponse.shipment

      };

    } catch (err) {
      console.log(err);
    }
  },
  computed:{
    ...mapGetters(["getCart","getCartTotalPrice","getHirePrice"]),
    ...mapState(["shippingPrice","shippingEstimatedDelivery","hireCount"])
  },
  name: "checkout",
  methods:{
    onDeleteProduct(product){
        this.$store.commit("removeProduct",product)
    },
    onhireSelected(hireCount) {
      this.$store.commit("sethireCount", {
        hireCount:hireCount
      });
    },
    async onChooseShipping(shipment,shipPrice) {
      try {
        let response = await this.$axios.$post("/api/shipment", {
          shipment: shipment,
          shipPrice: shipPrice
        });

        this.$store.commit("setShipping", {
          price: response.shipment.price,
          estimatedDelivery: response.shipment.estimated
        });

        this.shippingPrice = response.shipment.price;
        this.estimatedDelivery = response.shipment.estimated;
      } catch (err) {}
    },
    async onBought(){
      let totalCost=this.getHirePrice+this.getCartTotalPrice+this.shippingPrice
      let hireCost=this.getHirePrice
      let shipCost=this.shippingPrice
      let user_credit=this.$auth.$state.user.credit
      if (user_credit>totalCost){
        try{
          let response = await this.$axios.$post("/api/order", {
            totalCost: totalCost,
            cart: this.getCart,
            hireCount:this.hireCount,
            hireCost:hireCost,
            shipCost:shipCost,
            shipType:this.shipType
          });
          if (response.success){
            await this.$router.push("/")
            await this.$store.commit("clearCart")
          }
        }catch (err) {
          console.log(err);
        }
      }else{
        this.isCredit_Sufficent=false
      }
    }
  }
}
</script>

<style scoped>

</style>
