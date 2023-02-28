<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col">
        <div>
          <h1 class="text-center mb-5">Hello {{this.$auth.$state.user.name}}</h1>
          <form>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>Name:</strong> {{this.$auth.$state.user.name}}</label>
                  <input type="text" class="form-control" v-model="name" /></div>
              </div>
            </div>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>Email:</strong> {{this.$auth.$state.user.email}}</label><input class="form-control" type="text" v-model="email" /></div>
              </div>
            </div>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>Password:</strong></label><input type="text" class="form-control" v-model="password"/></div>
              </div>
            </div>
            <div class="form-row">
              <div class="col">
                <div class="form-group"><label><strong>Your Credit: </strong></label>  {{this.$auth.$state.user.credit}}</div>
              </div>
            </div>
            <div class="form-row row-cols-2">
              <div class="col text-right">
                <div class="card" v-if="mailUsed">
                  <div class="card text-danger border-primary border rounded-0">
                    <p class="m-auto">This mail is used</p>
                  </div>
                </div>
              </div>
              <div class="col text-right"><button class="btn btn-primary" type="button" @click="update">Update</button></div>
            </div>


            <div class="form-group text-left"><label><strong>Address</strong></label>

              <div class="col text-right" v-if="address==null">
                <n-link to="/address/add">
                  <font-awesome-icon :icon="['fas','plus']"/>
                  address
                </n-link>
              </div>
              <div class="form-row" v-if="address!=null">
              <div class="col text-right">
                  <div class="card">
                    <div class="card-body text-right">
                      <p class="text-left card-text">{{address.fullAddress}}</p>
                      <h6 class="text-left text-muted card-subtitle mb-2"><font-awesome-icon :icon="['fas','mail-bulk']"/>
                        {{ address.postalCode }}</h6>
                      <h6 class="text-right text-muted card-subtitle mb-2">{{ address.phoneNumber }}  <font-awesome-icon :icon="['fas','phone']"/></h6>
                      <h4 class="text-left card-title">{{ address.county.name }}</h4>
                      <nuxt-link to="/address" class="card-link text-right" >change Address</nuxt-link>
                    </div>
                  </div>
                </div>
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
  async asyncData({$axios, params}) {
    try {
      let response= await $axios.$get(`http://localhost:3000/api/addresses/${params.id}`)
      return {
        address:response.address
      };

    } catch (err) {
      console.log(err);
    }
  },
  data() {
    return {
      name: "",
      email:"",
      password:"",
      mailUsed:false
    }
  },
  name: "profile",
  methods:{
    async update() {
      let data = {
        name: this.name,
        email: this.email,
        password: this.password
      };
      try {
        let response = await this.$axios.$put("http://localhost:3000/api/auth/user", data);

        if (response.success) {
          this.name = "";
          this.email = "";
          this.password = "";

          await this.$auth.fetchUser();
        }
      } catch (err) {
        console.log(err);
        this.mailUsed=true

      }
    }
  }
}
</script>

<style scoped>

</style>
