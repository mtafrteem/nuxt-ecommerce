<template>
  <div class="container mt-custom mb-3">
    <div class="fade-in">
      <div class="row justify-content-end mr-5">
        <div class="col-md-5">
          <div class="card-group">
            <div class="card border-top-orange border-0 shadow-lg py-3 px-2 rounded">
              <div class="card-body">
                <h4 class="">Daftar Sekarang</h4>
                <hr />
                <div v-if="validation.message" class="mt-2">
                  <b-alert show variant="danger">{{
                    validation.message
                  }}</b-alert>
                </div>
                <form @submit.prevent="register">
                  <div class="input-group mb-3">
                    <div class="input-group-prepend">
                      <span class="input-group-text">
                        <i class="fa fa-user"></i>
                      </span>
                    </div>
                    <input
                      class="form-control"
                      v-model="user.name"
                      type="text"
                      placeholder="Nama Lengkap"
                    />
                  </div>
                  <div v-if="validation.name" class="mt-2">
                    <b-alert show variant="danger">{{
                      validation.name[0]
                    }}</b-alert>
                  </div>
                  <div class="input-group mb-3">
                    <div class="input-group-prepend">
                      <span class="input-group-text">
                        <i class="fa fa-envelope"></i>
                      </span>
                    </div>
                    <input
                      class="form-control"
                      v-model="user.email"
                      type="email"
                      placeholder="Alamat email"
                    />
                  </div>
                  <div v-if="validation.email" class="mt-2">
                    <b-alert show variant="danger">{{
                      validation.email[0]
                    }}</b-alert>
                  </div>

                  <div class="input-group mb-4">
                    <div class="input-group-prepend">
                      <span class="input-group-text">
                        <i class="fa fa-lock"></i>
                      </span>
                    </div>
                    <input
                      class="form-control"
                      v-model="user.password"
                      type="password"
                      placeholder="Masukkan sandi"
                    />
                  </div>
                  <div v-if="validation.password" class="mt-2">
                    <b-alert show variant="danger">{{
                      validation.password[0]
                    }}</b-alert>
                  </div>
                  <div class="input-group mb-4">
                    <div class="input-group-prepend">
                      <span class="input-group-text">
                        <i class="fa fa-lock"></i>
                      </span>
                    </div>
                    <input
                      class="form-control"
                      v-model="user.password_confirmation"
                      type="password"
                      placeholder="Ulangi sandi"
                    />
                  </div>

                  <div class="row">
                    <div class="col-12">
                      <button
                        class="
                          btn btn-warning btn-block
                          shadow-sm
                          rounded-sm
                          px-5
                          btn-lg
                        "
                        type="submit"
                      >
                        DAFTAR
                      </button>
                      <!-- <button
                        class="btn btn-warning shadow-sm rounded-sm px-4"
                        type="reset"
                      >
                        RESET
                      </button> -->
                    </div>
                  </div>
                </form>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="text-right mt-4 mr-akun">
        Punya akun?
        <nuxt-link :to="{ name: 'customer-login' }" class="font-weight-bold">
          Log in
        </nuxt-link>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  //middleware
  middleware: "authenticated",

  //layout
  layout: "authweb",

  //meta
  head() {
    return {
      title: "Daftar | Tokobiru",
    };
  },

  //data function
  data() {
    return {
      //state user
      user: {
        name: "",
        email: "",
        password: "",
        password_confirmation: "",
      },
      //validation
      validation: [],
    };
  },

  //method
  methods: {
    //method "register"
    async register() {
      //dispatch to action "storeRegister"
      await this.$store
        .dispatch("customer/customer/storeRegister", {
          name: this.user.name,
          email: this.user.email,
          password: this.user.password,
          password_confirmation: this.user.password_confirmation,
        })

        .then(() => {
          //sweet alert
          this.$swal.fire({
            title: "REGISTER BERHASIL!",
            text: "Proses Register Berhasil!",
            icon: "success",
            showConfirmButton: false,
            timer: 2000,
          });

          //redirect
          this.$router.push({
            name: "customer-login",
          });
        })

        .catch((error) => {
          //assign validation
          this.validation = error.response.data;
        });
    },
  },
};
</script>

<style>
</style>