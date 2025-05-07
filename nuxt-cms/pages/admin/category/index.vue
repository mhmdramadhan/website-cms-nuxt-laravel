<template>
  <div class="content-wrapper mb-5">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <div class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-folder"></i> CATEGORIES
          </h3>
          <div class="card-tools">
            <button
              type="button"
              class="btn btn-tool"
              data-card-widget="collapse"
              title="Collapse"
            >
              <i class="fas fa-minus"></i>
            </button>
            <button
              type="button"
              class="btn btn-tool"
              data-card-widget="remove"
              title="Remove"
            >
              <i class="fas fa-times"></i>
            </button>
          </div>
        </div>
        <div class="card-body">
          <div class="form-group">
            <div class="input-group mb-3">
              <div class="input-group-prepend">
                <nuxt-link
                  :to="{ name: 'admin-category-create' }"
                  class="btn btn-info btn-sm"
                  style="padding-top: 8px"
                >
                  <i class="fa fa-plus-circle"></i> TAMBAH</nuxt-link
                >
              </div>
              <input
                type="text"
                class="form-control"
                v-model="search"
                @keypress.enter="searchData"
                placeholder="cari berdasarkan nama tag"
              />
              <div class="input-group-append">
                <button @click="searchData" class="btn btn-info">
                  <i class="fa fa-search"></i>
                  CARI
                </button>
              </div>
            </div>
          </div>

          <!-- table -->
          <b-table
            striped
            bordered
            hover
            :items="categories"
            :fields="fields"
            show-empty
          >
            <template v-slot:cell(image)="data">
              <img class="img-fluid" width="50" :src="data.item.image" />
            </template>
            <template v-slot:cell(actions)="row">
              <b-button
                :to="{
                  name: 'admin-category-edit-id',
                  params: { id: row.item.id },
                }"
                size="sm"
                variant="warning"
              >
                <i class="fas fa-edit"></i>
              </b-button>
              <b-button
                variant="danger"
                size="sm"
                @click="deleteTag(row.item.id)"
              >
                <i class="fas fa-trash"></i>
              </b-button>
            </template>
          </b-table>

          <!-- pagination -->
          <b-pagination
            v-model="pagination.current_page"
            :total-rows="pagination.total"
            :per-page="pagination.per_page"
            @change="changePage"
          >
            <template #first-text>
              <i class="fas fa-angle-double-left"></i>
            </template>
            <template #prev-text>
              <i class="fas fa-angle-left"></i>
            </template>
            <template #next-text>
              <i class="fas fa-angle-right"></i>
            </template>
            <template #last-text>
              <i class="fas fa-angle-double-right"></i>
            </template>
            <template #page-text="{ page }">
              <span>{{ page }}</span>
            </template>
            <template #page-link="{ page, isActive }">
              <b-button
                :class="isActive ? 'active' : ''"
                @click="changePage(page)"
                size="sm"
                variant="link"
              >
                {{ page }}
              </b-button>
            </template>
          </b-pagination>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  // layout
  layout: "admin",

  // meta
  head() {
    return {
      title: "Kategori",
      meta: [
        { name: "description", content: "Kategori" },
        { name: "keywords", content: "Kategori, Kategori" },
      ],
    };
  },

  data() {
    return {
      fields: [
        { key: "image", label: "GAMBAR" },
        { key: "name", label: "NAMA Kategori" },
        { key: "actions", label: "AKSI" },
      ],

      //   search
      search: "",
    };
  },

  // watch query url
  watchQuery: ["q", "page"],

  async asyncData({ $axios, query }) {
    // page
    let page = query.page ? parseInt(query.page) : "";

    // search
    let search = query.q ? query.q : "";

    const categories = await $axios.$get(
      `/api/admin/categories?q=${search}&page=${page}`
    );

    console.log(categories.data.data);

    return {
      categories: categories.data.data,
      pagination: categories.data,
    };
  },

  methods: {
    // change page pagination
    changePage(page) {
      this.$router.push({
        path: this.$route.path,
        query: {
          q: this.$route.query.q,
          page: page,
        },
      });
    },

    // search data
    searchData() {
      this.$router.push({
        path: this.$route.path,
        query: {
          q: this.search,
        },
      });
    },

    // delete tag
    deleteTag(id) {
      this.$swal
        .fire({
          title: "Hapus Tag",
          text: "Apakah anda yakin ingin menghapus tag ini?",
          icon: "warning",
          showCancelButton: true,
          confirmButtonText: "Ya, hapus!",
          cancelButtonText: "Tidak, batalkan!",
        })
        .then((result) => {
          if (result.isConfirmed) {
            this.$axios
              .delete(`/api/admin/categories/${id}`)
              .then(() => {
                // refresh data
                this.$nuxt.refresh();

                // alert
                this.$swal.fire({
                  title: "Berhasil!",
                  text: "Tag berhasil dihapus.",
                  icon: "success",
                  showCancelButton: false,
                  timer: 1500,
                });
              })
              .catch(() => {
                this.$swal.fire("Gagal!", "Tag gagal dihapus.", "error");
              });
          }
        });
    },
  },
};
</script>