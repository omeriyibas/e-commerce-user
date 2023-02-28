<template>
  <div class="form-container mt-5">
    <div class="col-2 m-auto">
      <h2 class="text-center mb-5"><strong>Create</strong> an account.</h2>
      <div class="form-group"><input type="text" class="form-control" placeholder="Email" v-model="email"/></div>
      <div class="form-group"><input type="password" class="form-control" placeholder="Password" v-model="password"/>
      </div>
      <div class="form-group">
        <button class="btn btn-primary btn-block" @click="login"> Login</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  middleware: "auth",
  auth: "guest",
  layout: "none",
  data() {
    return {
      email: "",
      password: "",
      statePassword:"",
    }
  },
  methods: {
    async login() {
      try {
        let data = {
          email: this.email,
          password: this.password
        }
        let response = await this.$axios.$post("http://localhost:3000/api/auth/login", data)
        if (response.success) {
          this.$auth.loginWith('local', {
            data: {
              email: this.email,
              password: this.password
            }
          });

        }
      } catch (err) {
        console.log(err)

      }
    }
  }
}
</script>

<style>

</style>
