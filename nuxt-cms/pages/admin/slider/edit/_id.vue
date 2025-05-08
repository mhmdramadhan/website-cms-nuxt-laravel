<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <section class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> EDIT SLIDER
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
          <form @submit.prevent="updateSlider">
            <div class="form-group">
              <label>GAMBAR SLIDER</label>
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
      title: "Tambah Kategori",
      meta: [
        { name: "description", content: "Tambah Kategori" },
        { name: "keywords", content: "Kategori, Tambah Kategori" },
      ],
    };
  },

  data() {
    return {
      slider: {
        image: null,
      },
      validation: [],
      imagePreview: null,
    };
  },

  async mounted() {
    try {
      const response = await this.$axios.get(
        `/api/admin/sliders/${this.$route.params.id}`
      );
      console.log(response.data.data);
      
      this.imagePreview = response.data.data.image;
    } catch (error) {
      console.error("Error fetching category data:", error);
    }
  },

  methods: {
    onFileChange(event) {
      const file = event.target.files[0];
      this.slider.image = file;

      const reader = new FileReader();
      reader.onload = (e) => {
        this.imagePreview = e.target.result;
      };
      reader.readAsDataURL(file);
    },

    async updateSlider() {
      try {
        const formData = new FormData();
        formData.append("image", this.slider.image);
        formData.append("_method", "PATCH");

        const response = await this.$axios.post(
          "/api/admin/sliders",
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
          text: "Slider berhasil dirubah!",
          icon: "success",
          showCancelButton: false,
          timer: 2000,
        });

        // redirect, if success
        this.$router.push({ name: "admin-slider" });
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