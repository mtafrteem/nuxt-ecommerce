<template>
  <div class="c-app">
    <header class="section-header fixed-top">
      <section class="header-main border-bottom py-3">
        <div class="container">
          <div class="row align-items-center">
            <div class="d-flex col justify-content-start">
              <nuxt-link to="/" class="brand-wrap" data-abc="true">
                <img src="/images/tokobiru.png" width="150" class="" />
              </nuxt-link>
            </div>
            <div class="d-flex col justify-content-end text-white">Butuh bantuan?</div>
          </div>
        </div>
      </section>
    </header>
    <!-- content -->
    <Nuxt />
    <!-- end content -->
  </div>
</template>

<script>
export default {};
</script>

<style>
.c-app {
  background-color: #fff !important;
}
</style>