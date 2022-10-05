<template>
  <div class="container mt-custom mb-5">
    <div class="fade-in">
      <div class="row">
        <div class="col-md-4 mb-4">
          <div class="card border-0">
            <div class="card-body">
              <img :src="product.image" class="w-100 rounded" />
            </div>
          </div>
        </div>

        <div class="col-md-5">
          <div class="card border-0">
            <div class="card-body">
              <h4 class="mb-4 font-weight-semibold">{{ product.title }}</h4>
              <h5 class="mb-0 font-weight-semibold">
                <s class="text-red">Rp{{ formatPrice(product.price) }}</s> /
                <strong>{{ product.discount }}%</strong>
              </h5>
              <h1 class="mb-4 font-weight-bolder mt-2 text-success">
                Rp{{ formatPrice(calculateDiscount(product)) }}
              </h1>
              <hr />
              <div class="mt-4">
                <p class="mb-1">Kondisi: <strong>Baru</strong></p>
                <p class="mb-1">
                  Berat satuan: <strong>{{ product.weight }} gram</strong>
                </p>
                <p>
                  Kategori:
                  <nuxt-link
                    :to="{
                      name: 'categories-slug',
                      params: { slug: product.category.slug },
                    }"
                    class=""
                    data-abc="true"
                    >{{ product.category.name }}</nuxt-link
                  >
                </p>
                <h5>Garansi Resmi dari Tokobiru!</h5>
                <div v-html="product.description"></div>
              </div>
            </div>
          </div>
        </div>

        <div class="col-md-3 sticky-top">
          <div class="card border-1 rounded">
            <div class="card-body">
              <h5 class="font-weight-semibold mb-3">Pilih Varian</h5>
              <form>
                <div class="form-group">
                  <label for="exampleFormControlSelect1">Warna</label>
                  <select class="form-control" id="exampleFormControlSelect1">
                    <option>contoh</option>
                    <option>contoh</option>
                    <option>contoh</option>
                  </select>
                </div>
                <div class="form-group">
                  <label for="exampleFormControlSelect1">Ukuran</label>
                  <select class="form-control" id="exampleFormControlSelect1">
                    <option>contoh</option>
                    <option>contoh</option>
                    <option>contoh</option>
                  </select>
                </div>
              </form>
              <p class="mb-2">
                Stok produk: <strong>{{ product.stock }}</strong>
              </p>
              <hr />
              <button
                @click="
                  addToCart(
                    product.id,
                    calculateDiscount(product),
                    product.weight
                  )
                "
                class="btn btn-block btn-warning border-0 py-2 mt-2"
              >
                + Keranjang
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="row mt-4">
        <div class="col-md-12">
          <div class="card border-0 rounded">
            <div class="card-body">
              <h5>
                ULASAN PRODUK (
                <strong>{{ product.reviews_count }}</strong> ulasan )
              </h5>
              <hr />
              <div
                class="card bg-light rounded"
                v-for="review in product.reviews"
                :key="review.id"
              >
                <div class="card-body">
                  <div class="row">
                    <div class="col-md-1">
                      <div class="review-avatar avatar-sm">
                        <img
                          :src="`https://ui-avatars.com/api/?name=${review.customer.name}&amp;background=4e73df&amp;color=ffffff&amp;size=100`"
                        />
                      </div>
                    </div>
                    <div class="col-md-11">
                      <client-only>
                        <vue-star-rating
                          class="mb-2"
                          :rating="review.rating"
                          :star-size="20"
                          :read-only="true"
                          :show-rating="false"
                        >
                        </vue-star-rating>
                      </client-only>
                      <strong>
                        <span class="text-dark">{{
                          review.customer.name
                        }}</span>
                      </strong>
                      <div class="description mt-2">
                        <span
                          style="
                            color: rgb(119, 118, 118);
                            font-size: 15px;
                            font-style: italic;
                          "
                        >
                          {{ review.review }}
                        </span>
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
export default {
  //meta
  head() {
    return {
      title: `${this.product.title} - Tokobiru | Situs Belanja Online Terpercaya!`,
      meta: [
        {
          hid: "og:title",
          name: "og:title",
          content: `${this.product.title} - Tokobiru | Situs Belanja Online Terpercaya!`,
        },
        {
          hid: "og:site_name",
          name: "og:site_name",
          content: `${this.product.title} - Tokobiru | Situs Belanja Online Terpercaya!`,
        },
        {
          hid: "og:image",
          name: "og:image",
          content: this.product.image,
        },
        {
          hid: "description",
          name: "description",
          content: `${this.product.title.substr(0, 30)}...`,
        },
      ],
    };
  },

  //hook "asyncData"
  async asyncData({ store, route }) {
    await store.dispatch("web/product/getDetailProduct", route.params.slug);
  },

  //computed
  computed: {
    //product
    product() {
      return this.$store.state.web.product.product;
    },
  },

  //method
  methods: {
    //method "addToCart"
    async addToCart(productId, price, weight) {
      //check loggedIn "false"
      if (!this.$auth.loggedIn) {
        //redirect
        return this.$router.push({
          name: "customer-login",
        });
      }

      //check customer role
      if (this.$auth.strategy.name != "customer") {
        //redirect
        return this.$router.push({
          name: "customer-login",
        });
      }

      //dispatch to action "storeCart" vuex
      await this.$store
        .dispatch("web/cart/storeCart", {
          product_id: productId,
          price: price,
          qty: 1,
          weight: weight,
        })

        //success add to cart
        .then(() => {
          //sweet alert
          this.$swal.fire({
            title: "BERHASIL!",
            text: "Produk Berhasil Ditambahkan ke Keranjang!",
            icon: "success",
            showConfirmButton: false,
            timer: 3000,
          });
        });
    },
  },
};
</script>

<style>
</style>