<template>
  <div>

    <div class="row">
      <div class="col text-center mt-2">
        <h1>Your Orders</h1>
        <hr>
      </div>
    </div>
        <div class="row row-cols-1" v-for="order in orders" :key="order._id">
          <div class="col">
            <div class="container mt-2">
              <div class="card">
                <div class="card-body">
                  <div class="row row-cols-2">
                    <div class="col-7">
                      <h4>Order No: <label class="small">{{order._id}}</label></h4>
                      <h5 class="text-muted mb-2">order Date: <label class="small">{{order.orderDate}}</label></h5>
                      <hr />
                      <h6 class="text-muted mb-2" v-for="product in order.products" :key="product._id">{{ product.productName }} x
                        {{ product.quantity }} = {{ product.quantity*product.price }}$<br /></h6>
                      <hr />
                      <p></p>
                      <h3 class="text-left" v-if="order.orderStatus=='prepearing'"><font-awesome-icon :icon="['fas','box-open']" class="text-warning"/>
                        order is prepearing</h3>
                      <h3 class="text-left" v-if="order.orderStatus=='shipped'"><font-awesome-icon :icon="['fas','box']" class="text-success"/>
                        order is shipped</h3>
                    </div>
                    <div class="col-4 offset-1 align-self-center">
                      <h6 class="text-muted card-subtitle mb-2">Shipping Cost:{{order.shipType}} - {{order.shipCost}}$</h6>
                      <h6 class="text-muted card-subtitle mb-2 mt-3">Interest Cost: {{order.hireCount}} x {{Math.round(order.hireCost/order.hireCount*100)/100}} = {{order.hireCost}}$ </h6>
                      <hr />
                      <h5 class="text-muted card-subtitle mb-2">Total Cost: {{order.hireCount}} x {{Math.round(order.totalCost/order.hireCount*100)/100}} = {{order.totalCost}}$</h5>
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
export default {

  name: "index",
  async asyncData({ $axios }) {
    try {
      let response = await $axios.$get("/api/user/orders");

      return {
        orders: response.orders
      };
    } catch (err) {
      console.log(err);
    }
  },
}
</script>

<style scoped>

</style>
