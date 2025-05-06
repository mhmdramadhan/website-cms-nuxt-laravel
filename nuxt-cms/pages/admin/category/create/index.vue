<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <section class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> TAMBAH KATEGORI
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
          <form @submit.prevent="storeCategory">
            <div class="form-group">
              <label>NAMA KATEGORI</label>
              <input
                type="text"
                v-model="category.name"
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
              <label>GAMBAR KATEGORI</label>
              <input type="file" @change="onFileChange" class="form-control" />
              <div v-if="validation.image" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.image[0]
                }}</b-alert>
              </div>
            </div>
            <div v-if="imagePreview" class="mt-2">
              <img
                :src="imagePreview"
                alt="Image Preview"
                class="img-thumbnail"
              />
            </div>
            <button class="btn btn-info mr-1 btn-submit" type="submit">
              <i class="fa fa-paper-plane"></i> SIMPAN
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
      title: "Tambah Kategori",
      meta: [
        { name: "description", content: "Tambah Kategori" },
        { name: "keywords", content: "Kategori, Tambah Kategori" },
      ],
    };
  },

  data() {
    return {
      category: {
        name: "",
        image: null,
      },
      validation: [],
      imagePreview: null,
    };
  },

  methods: {
    onFileChange(event) {
      const file = event.target.files[0];
      this.category.image = file;

      const reader = new FileReader();
      reader.onload = (e) => {
        this.imagePreview = e.target.result;
      };
      reader.readAsDataURL(file);
    },

    async storeCategory() {
      try {
        const formData = new FormData();
        formData.append("name", this.category.name);
        formData.append("image", this.category.image);

        const response = await this.$axios.post(
          "/api/admin/categories",
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
          text: "Kategori berhasil ditambahkan",
          icon: "success",
          showCancelButton: false,
          timer: 2000,
        });

        // redirect, if success
        this.$router.push({ name: "admin-category" });
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