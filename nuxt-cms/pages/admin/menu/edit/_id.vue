<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <section class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> EDIT MENU
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
          <form @submit.prevent="storeMenu">
            <div class="form-group">
              <label>NAMA</label>
              <input
                type="text"
                v-model="menu.name"
                placeholder="Masukkan Nama Kategori"
                class="form-control"
              />
              <div v-if="validation.name" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.name[0]
                }}</b-alert>
              </div>
            </div>
            <div class="form-group">
              <label>URL MENU</label>
              <input
                type="text"
                v-model="menu.url"
                placeholder="Masukkan Nama Kategori"
                class="form-control"
              />
              <div v-if="validation.name" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.name[0]
                }}</b-alert>
              </div>
            </div>
            <button class="btn btn-info mr-1 btn-submit" type="submit">
              <i class="fa fa-paper-plane"></i> UBAH
            </button>
            <button class="btn btn-warning btn-reset" type="reset">
              <i class="fa fa-redo"></i> RESET
            </button>
          </form>
        </div>
      </section>
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
      title: "Tambah Menu",
      meta: [
        { name: "description", content: "Tambah Menu" },
        { name: "keywords", content: "Menu, Tambah Menu" },
      ],
    };
  },

  data() {
    return {
      menu: {
        name: "",
        url: "",
      },
      validation: [],
    };
  },

  mounted() {
    // fetching data menu by id
    this.$axios
      .get(`/api/admin/menus/${this.$route.params.id}`)
      .then((response) => {
        this.menu = response.data.data;
      })
      .catch((error) => {
        console.error(error);
      });
  },

  methods: {
    async storeMenu() {
      try {
        const formData = new FormData();
        formData.append("name", this.menu.name);
        formData.append("url", this.menu.url);
        formData.append("_method", "PUT");

        const response = await this.$axios.post(
          "/api/admin/menus/" + this.$route.params.id,
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          }
        );

        // if (response.status === 200) {
        this.$swal.fire({
          title: "Berhasil",
          text: "Menu berhasil diubah",
          icon: "success",
          showCancelButton: false,
          timer: 2000,
        });

        // redirect, if success
        this.$router.push({ name: "admin-menu" });
        // }
      } catch (error) {
        if (error.response && error.response.status === 422) {
          this.validation = error.response.data;
        }
      }
    },
  },
};
</script>