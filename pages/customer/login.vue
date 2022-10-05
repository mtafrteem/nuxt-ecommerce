<template>
  <div class="container mt-custom mb-3">
    <div class="fade-in">
      <div class="row justify-content-end mr-5">
        <div class="col-md-5">
          <div class="card-group">
            <div
              class="
                card
                border-top-orange border-0
                shadow-lg
                py-3
                px-2
                rounded
              "
            >
              <div class="card-body">
                <h4>Log in</h4>
                <hr />
                <div v-if="validation.message" class="mt-2">
                  <b-alert show variant="danger">{{
                    validation.message
                  }}</b-alert>
                </div>
                <form @submit.prevent="login">
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
                      placeholder="Kata sandi"
                    />
                  </div>
                  <div v-if="validation.password" class="mt-2">
                    <b-alert show variant="danger">{{
                      validation.password[0]
                    }}</b-alert>
                  </div>
                  <div class="row">
                    <div class="col-12">
                      <button
                        class="
                          btn btn-warning btn-lg
                          shadow-sm
                          rounded-sm
                          px-5
                          w-100
                        "
                        type="submit"
                      >
                        LOG IN
                      </button>
                    </div>
                  </div>
                </form>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="text-right mt-4 mr-login">
        Baru di Tokobiru?
        <nuxt-link :to="{ name: 'customer-register' }" class="font-weight-bold"
          >Daftar</nuxt-link
        >
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
      title: "Login | Tokobiru",
    };
  },

  data() {
    return {
      //state user
      user: {
        email: "",
        password: "",
      },
      //validation
      validation: [],
    };
  },

  methods: {
    async login() {
      await this.$auth
        .loginWith("customer", {
          data: {
            email: this.user.email,
            password: this.user.password,
          },
        })

        .then(() => {
          //fething carts on Rest API
          this.$store.dispatch("web/cart/getCartsData");
          this.$store.dispatch("web/cart/getCartPrice");

          //redirect
          this.$router.push({
            name: "customer-dashboard",
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