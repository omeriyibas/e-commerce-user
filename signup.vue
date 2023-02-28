<template>
  <div>
    <div class="form-container mt-5">
      <div class="col-2 m-auto">
        <h2 class="text-center mb-5"><strong>Create</strong> an account.</h2>
        <div class="form-group"><input type="text" class="form-control" placeholder="Name" v-model="name"/></div>
        <div class="form-group"><input type="text" class="form-control" placeholder="Email" v-model="email"/></div>
        <div class="form-group"><input type="password" class="form-control" placeholder="Password" v-model="password"/>
        </div>
        <div class="form-group"><input type="password" class="form-control"
                                       placeholder="Password (repeat)" v-model="password2"/></div>
        <div class="form-group" v-if="notMatch===true">
          <div class="card text-danger border-primary border rounded-0">
            <p class="m-auto">Password Not Match</p>
          </div>
        </div>
        <div class="form-group" v-if="mailUsed===true">
          <div class="card text-danger border-primary border rounded-0">
            <p class="m-auto">This mail is used</p>
          </div>
        </div>
        <div class="form-group">
          <button class="btn btn-primary btn-block" @click="register">Sign Up</button>
        </div>
        <nuxt-link to="/signin">
          You already have an account?
          <br>
          Login here.
        </nuxt-link>
        <hr>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  middleware: "auth",
  auth: "guest",
  name: "signup",
  layout: "none",
  data() {
    return {
      name: "",
      email: "",
      password: "",
      password2: "",
      notMatch: false,
      mailUsed: false,
    }
  },
  methods: {
    async register() {
      if (this.password !== this.password2) {
        this.notMatch = true
      } else {
        try {
          let data = {
            name: this.name,
            email: this.email,
            password: this.password,
          }
          let response = await this.$axios.$post("http://localhost:3000/api/auth/signup", data)
          console.log(response)

          if (response.success) {
            this.$auth.loginWith('local', {
              data: {
                email: this.email,
                password: this.password
              }
            });
            await this.$router.push("/")
          }
        } catch (err) {
          console.log(err)
          this.mailUsed = true
        }
      }

    }
  }
}
</script>

<style>

</style>
