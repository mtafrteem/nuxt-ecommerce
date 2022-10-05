<template>
  <div class="container mt-custom">
    <div class="fade-in">
      <div class="row">
        <div class="col-lg-12">
          <div class="card border-0 rounded shadow-sm border-top-orange">
            <div class="card-body">
              <h5><i class="fa fa-tachometer-alt"></i> Daftar Transaksi</h5>
              <hr />

              <div class="row">
                <div class="col-md-12">
                  <div class="alert alert-success" role="alert">
                    Selamat Datang <strong>{{ $auth.user.name }}</strong>
                  </div>
                </div>
              </div>

              <div class="row">
                <div class="col-6 col-lg-3">
                  <div class="card rounded shadow-sm overflow-hidden">
                    <div class="card-body p-0 d-flex align-items-center">
                      <div class="bg-primary py-4 px-5 mfe-3">
                        <i class="fas fa-circle-notch fa-spin fa-2x"></i>
                      </div>
                      <div>
                        <div class="text-value text-primary">{{ pending }}</div>
                        <div
                          class="
                            text-muted text-uppercase
                            font-weight-bold
                            small
                          "
                        >
                          PENDING
                        </div>
                      </div>
                    </div>
                  </div>
                </div>

                <div class="col-6 col-lg-3">
                  <div class="card rounded shadow-sm overflow-hidden">
                    <div class="card-body p-0 d-flex align-items-center">
                      <div class="bg-success py-4 px-5 mfe-3">
                        <i class="fas fa-check-circle fa-2x"></i>
                      </div>
                      <div>
                        <div class="text-value text-success">{{ success }}</div>
                        <div
                          class="
                            text-muted text-uppercase
                            font-weight-bold
                            small
                          "
                        >
                          SUCCESS
                        </div>
                      </div>
                    </div>
                  </div>
                </div>

                <div class="col-6 col-lg-3">
                  <div class="card rounded shadow-sm overflow-hidden">
                    <div class="card-body p-0 d-flex align-items-center">
                      <div class="bg-warning py-4 px-5 mfe-3">
                        <i class="fas fa-exclamation-triangle fa-2x"></i>
                      </div>
                      <div>
                        <div class="text-value text-warning">{{ expired }}</div>
                        <div
                          class="
                            text-muted text-uppercase
                            font-weight-bold
                            small
                          "
                        >
                          EXPIRED
                        </div>
                      </div>
                    </div>
                  </div>
                </div>

                <div class="col-6 col-lg-3">
                  <div class="card rounded shadow-sm overflow-hidden">
                    <div class="card-body p-0 d-flex align-items-center">
                      <div class="bg-danger py-4 px-5 mfe-3">
                        <i class="fas fa-times-circle fa-2x"></i>
                      </div>
                      <div>
                        <div class="text-value text-danger">{{ failed }}</div>
                        <div
                          class="
                            text-muted text-uppercase
                            font-weight-bold
                            small
                          "
                        >
                          FAILED
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <div class="form-group">
                <div class="input-group mb-3">
                  <input
                    type="text"
                    class="form-control"
                    v-model="search"
                    @keypress.enter="searchData"
                    placeholder="cari berdasarkan no. invoice"
                  />
                  <div class="input-group-append">
                    <button @click="searchData" class="btn btn-warning">
                      <i class="fa fa-search"></i>
                      Cari
                    </button>
                  </div>
                </div>
              </div>

              <b-table
                striped
                bordered
                hover
                :items="invoices.data"
                :fields="fields"
                show-empty
              >
                <template v-slot:cell(grand_total)="row">
                  Rp{{ formatPrice(row.item.grand_total) }}
                </template>
                <template v-slot:cell(status)="row">
                  <button
                    v-if="row.item.status == 'pending'"
                    class="btn btn-sm btn-primary"
                  >
                    <i class="fa fa-circle-notch fa-spin"></i>
                    {{ row.item.status }}
                  </button>
                  <button
                    v-if="row.item.status == 'success'"
                    class="btn btn-sm btn-success"
                  >
                    <i class="fa fa-check-circle"></i> {{ row.item.status }}
                  </button>
                  <button
                    v-if="row.item.status == 'expired'"
                    class="btn btn-sm btn-warning-2"
                  >
                    <i class="fa fa-exclamation-triangle"></i>
                    {{ row.item.status }}
                  </button>
                  <button
                    v-if="row.item.status == 'failed'"
                    class="btn btn-sm btn-danger"
                  >
                    <i class="fa fa-times-circle"></i> {{ row.item.status }}
                  </button>
                </template>
                <template v-slot:cell(actions)="row">
                  <b-button
                    :to="{
                      name: 'customer-invoices-show-snap_token',
                      params: { snap_token: row.item.snap_token },
                    }"
                    variant="info"
                    size="sm"
                  >
                    Detail Transaksi
                  </b-button>
                </template>
              </b-table>

              <!-- pagination -->
              <b-pagination
                align="right"
                :value="invoices.current_page"
                :total-rows="invoices.total"
                :per-page="invoices.per_page"
                @change="changePage"
                aria-controls="my-table"
              ></b-pagination>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  //middleware
  middleware: "isCustomer",

  //layout
  layout: "default",

  //meta
  head() {
    return {
      title: "Dashboard - Customer",
    };
  },

  //data function
  data() {
    return {
      //table header
      fields: [
        {
          label: "No. Invoice",
          key: "invoice",
        },
        {
          label: "Total Belanja",
          key: "grand_total",
        },
        {
          label: "Status Pembayaran",
          key: "status",
        },
        {
          label: "Aksi",
          key: "actions",
        },
      ],

      //state search
      search: "",
    };
  },

  async asyncData({ $axios }) {
    //fetching dashboard
    const dashboard = await $axios.$get("/api/customer/dashboard");

    return {
      //count statistik
      pending: dashboard.data.count.pending,
      success: dashboard.data.count.success,
      expired: dashboard.data.count.expired,
      failed: dashboard.data.count.failed,
    };
  },

  //hook "asyncData"
  async asyncData({ store }) {
    await store.dispatch("customer/invoice/getInvoicesData");
  },

  //computed
  computed: {
    //invoices
    invoices() {
      return this.$store.state.customer.invoice.invoices;
    },
  },

  //method
  methods: {
    //method "searchData"
    searchData() {
      //commit to mutation "SET_PAGE"
      this.$store.commit("customer/invoice/SET_PAGE", 1);

      //dispatch on action "getInvoicesData"
      this.$store.dispatch("customer/invoice/getInvoicesData", this.search);
    },

    //method "changePage"
    changePage(page) {
      //commit to mutation "SET_PAGE"
      this.$store.commit("customer/invoice/SET_PAGE", page);

      //dispatch on action "getInvoicesData"
      this.$store.dispatch("customer/invoice/getInvoicesData", this.search);
    },
  },
};
</script>

<style>
</style>