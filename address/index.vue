<template>
  <div>

    <div class="row">
      <div class="col text-center mt-2">
        <h1>Your Address</h1>
        <hr>
      </div>
    </div>
      <div class="container mt-5">
        <div class="col text-right">
          <n-link to="/address/add">
            <font-awesome-icon :icon="['fas','plus']"/>
            address
          </n-link>
        </div>

        <div class="form-row mt-2" v-for="(address,index) in addresses" :key="address._id">
          <div class="col text-right">
            <div class="card">
              <div class="card-body text-right">
                <p class="text-left card-text">{{address.fullAddress}}</p>
                <h6 class="text-left text-muted card-subtitle mb-2">
                  <font-awesome-icon :icon="['fas','mail-bulk']"/>
                  {{ address.postalCode }}
                </h6>
                <h6 class="text-right text-muted card-subtitle mb-2">{{ address.phoneNumber }}
                  <font-awesome-icon :icon="['fas','phone']"/>
                </h6>
                <h4 class="text-left card-title">{{address.county.name}}</h4>
                <b-link @click="onSetDefault(address._id)" class="card-link text-right text-success" v-if="address._id!==$auth.user.address" >set Default</b-link>
                <b-link @click="onDeleteAddress(address._id,index)" class="card-link text-right text-danger">remove Address</b-link>
                <n-link :to="`/address/${address._id}`" class="card-link text-right">update Address</n-link>
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
      let response = await $axios.$get("/api/user/addresses");

      return {
        addresses: response.address
      };
    } catch (err) {
      console.log(err);
    }
  },
  methods:{
    async onDeleteAddress(id, index) {
      try {
        let response = await this.$axios.$delete(`/api/addresses/${id}`);

        if (response.success) {
          this.message = response.message;
          this.addresses.splice(index, 1);
        }
      } catch (err) {
        this.message = err.message;
        console.log(err);
      }
    },
    async onSetDefault(id) {
      try {
        let response = await this.$axios.$put(`/api/addresses/set/default`, {
          id: id
        });

        if (response.success) {
          this.message = response.message;
          await this.$auth.fetchUser();
        }
      } catch (err) {
        this.message = err.message;
        console.log(err);
      }
      await this.$router.push("/");
    }
  }
}
</script>

<style scoped>

</style>
