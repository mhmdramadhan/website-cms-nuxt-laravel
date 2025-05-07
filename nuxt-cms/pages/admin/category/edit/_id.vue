<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <section class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> Edit KATEGORI
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
          <form @submit.prevent="updateCategory">
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
              <i class="fa fa-paper-plane"></i> UPDATE
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
  layout: "admin",

  //   meta
  head() {
    return {
      title: "Edit Kategori",
      meta: [
        { name: "description", content: "Edit Kategori" },
        { name: "keywords", content: "Edit Kategori" },
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

  mounted() {
    // fetching data category by id
    this.$axios
      .get(`/api/admin/categories/${this.$route.params.id}`)
      .then((response) => {
        this.category.name = response.data.data.name;

        // imagePreview
        this.imagePreview = response.data.data.image;
      })
      .catch((error) => {
        console.error(error);
      });
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

    updateCategory() {
      const formData = new FormData();
      formData.append("image", this.category.image);
      formData.append("name", this.category.name);
      formData.append("_method", "PATCH");

      console.log(formData);
      

      this.$axios
        .post(`/api/admin/categories/${this.$route.params.id}`, formData)
        .then((response) => {
          // sweet alert
          this.$swal.fire({
            title: "Berhasil",
            text: "Kategori Berhasil Diperbarui",
            icon: "success",
            showCancelButton: false,
            timer: 2000,
          });

          this.$router.push({
            name: "admin-category",
          });
        })
        .catch((error) => {
          if (error.response.status === 422) {
            this.validation = error.response.data;
          }
        });
    },
  },
};
</script>