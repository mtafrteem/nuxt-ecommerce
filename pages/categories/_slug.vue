<template>
  <div class="container mt-custom">
    <div class="fade-in">
      <div class="row">
        <div class="col-md-12 mb-2">
          <h5>
            Kategori : <strong>{{ category.name }}</strong>
          </h5>
        </div>
      </div>
      <div class="row">
        <div
          class="col-md-3 mt-1 mb-4"
          v-for="product in category.products"
          :key="product.id"
        >
          <div class="card h-100 border-0 rounded shadow">
              <div class="card-body">
                <div class="card-img-actions">
                  <img :src="product.image" class="card-img img-fluid" />
                </div>
              </div>
              <div class="card-body bg-light-custom text-center rounded-bottom">
                <div class="mb-2">
                  <h5 class="font-weight-semibold">
                    <nuxt-link
                      :to="{
                        name: 'products-slug',
                        params: { slug: product.slug },
                      }"
                      class="text-default"
                      data-abc="true"
                      >{{ product.title }}</nuxt-link
                    >
                  </h5>
                  <nuxt-link
                    :to="{
                      name: 'categories-slug',
                      params: { slug: product.category.slug },
                    }"
                    class="text-muted sub"
                    data-abc="true"
                    >{{ product.category.name }}</nuxt-link
                  >
                </div>
                <h6 class="mb-0 font-weight-semibold">
                  <s class="text-red">Rp{{ formatPrice(product.price) }}</s> /
                  <strong>{{ product.discount }} %</strong>
                </h6>
                <h4 class="mb-0 font-weight-semibold mt-3 text-success">
                  Rp{{ formatPrice(calculateDiscount(product)) }}
                </h4>
                <hr />
                <client-only>
                  <vue-star-rating
                    :rating="parseFloat(product.reviews_avg_rating)"
                    :increment="0.5"
                    :star-size="20"
                    :read-only="true"
                    :show-rating="false"
                    :inline="true"
                  ></vue-star-rating>
                  (<strong>{{ product.reviews_count }}</strong> ulasan)
                </client-only>
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
      title: `Kategori : ${this.category.name}  - Tokobiru | Situs Belanja Online Terpercaya!`,
      meta: [
        {
          hid: "og:title",
          name: "og:title",
          content: `Kategori : ${this.category.name}  - Tokobiru | Situs Belanja Online Terpercaya!`,
        },
        {
          hid: "og:site_name",
          name: "og:site_name",
          content: `Kategori : ${this.category.name}  - Tokobiru | Situs Belanja Online Terpercaya!`,
        },
        {
          hid: "og:image",
          name: "og:image",
          content: `${this.category.image}`,
        },
        {
          hid: "description",
          name: "description",
          content: `Kategori : ${this.category.name}  - Tokobiru | Situs Belanja Online Terpercaya!`,
        },
      ],
    };
  },

  //hook "asyncData"
  async asyncData({ store, route }) {
    await store.dispatch("web/category/getDetailCategory", route.params.slug);
  },

  //computed
  computed: {
    //category
    category() {
      return this.$store.state.web.category.category;
    },
  },
};
</script>

<style>
</style>