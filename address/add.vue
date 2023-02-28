<template xmlns="http://www.w3.org/1999/html">
  <div class="container mt-5">
    <div class="row">
      <div class="col">
        <div>
          <h1 class="text-center mb-5">Add Address</h1>
          <form>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>County:</strong> </label>
                  <select class="form-control" v-model="selectedCounty">
                    <option v-for="county in counties" :value="county._id" :key="county._id">
                      {{ county.name }}
                    </option>
                  </select>

                </div>
              </div>
            </div>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>Postal Code:</strong></label><input type="tel"
                                                                                           class="form-control"
                                                                                           v-model="postCode"/></div>

              </div>
            </div>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>Phone:</strong> </label><input type="tel" class="form-control"
                                                                                      v-model="phone"/></div>
              </div>
            </div>
            <div class="form-row">
              <div class="col text-right">
                <div class="form-group text-left"><label><strong>Address:</strong></label><textarea
                  class="form-control form-control-lg" v-model="fullAddress"></textarea></div>
              </div>
            </div>

            <div class="form-row">
              <div class="col text-right">
                <button class="btn btn-primary" type="button" @click="onAddAddress">Add</button>
              </div>
            </div>

          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({$axios}) {
    try {
      let response = await $axios.$get("http://localhost:3000/api/counties");
      return {
        counties: response.counties
      };
    } catch (err) {
      console.log(err);
    }
  },
  data() {
    return {
      selectedCounty:"",
      postCode:"",
      phone:"",
      fullAddress:"",
    }
  },
  methods: {
    async onAddAddress() {
      try {
        let data = {
          countyID:this.selectedCounty,
          fullAddress:this.fullAddress,
          postalCode:this.postCode,
          phoneNumber:this.phone
        };

        let response = await this.$axios.$post("/api/address", data);

        if (response.success) {
          await this.$router.push("/");
        }
      } catch (err) {
        console.log(err);
      }
    }
  }
}
</script>

<style scoped>

</style>
