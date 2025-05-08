<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <section class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> Edit POSTS
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
          <form @submit.prevent="updatePosts">
            <div class="form-group">
              <label>Gambar Posts</label>
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

            <div class="form-group">
              <label>Judul Post</label>
              <input
                type="text"
                v-model="posts.title"
                placeholder="Masukkan Nama Kategori"
                class="form-control"
              />
              <div v-if="validation.title" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.title[0]
                }}</b-alert>
              </div>
            </div>

            <div class="form-group">
              <label for="kategoru">Category</label>
              <multiselect
                v-model="posts.category_id"
                :options="categories"
                label="name"
                track-by="id"
                :searchable="true"
              />
              <div v-if="validation.category_id" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.category_id[0]
                }}</b-alert>
              </div>
            </div>

            <div class="form-group">
              <label for="content">Konten Post</label>
              <client-only placeholder="Loading...">
                <ckeditor-nuxt v-model="posts.content" :config="editorConfig" />
              </client-only>
              <div v-if="validation.content" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.content[0]
                }}</b-alert>
              </div>
            </div>

            <div class="form-group">
              <label for="kategoru">Tags</label>
              <multiselect
                v-model="posts.tags"
                :options="tags"
                label="name"
                track-by="id"
                :multiple="true"
                :searchable="true"
              />
            </div>

            <div class="form-group">
              <label for="description">Description</label>
              <textarea
                v-model="posts.description"
                class="form-control"
                id="description"
                rows="5"
                placeholder="Masukkan Deskripsi Singkat"
              ></textarea>
              <div v-if="validation.description" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.description[0]
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
      title: "Tambah Posts",
      meta: [
        { name: "description", content: "Tambah Posts" },
        { name: "keywords", content: "Posts, Tambah Posts" },
      ],
    };
  },

  components: {
    "ckeditor-nuxt": () => {
      if (process.client) {
        return import("@blowstack/ckeditor-nuxt");
      }
    },
  },

  data() {
    return {
      posts: {
        image: "",
        title: "",
        category_id: "",
        content: "",
        description: "",
        tags: [],
      },
      categories: [],
      tags: [],
      validation: [],
      imagePreview: null,
      editorConfig: {
        removePlugins: ["Title"],
        height: 500,
        simpleUpload: {
          uploadUrl: "http://localhost:8000/api/web/posts/storeImage",
        },
      },
    };
  },

  mounted() {
    this.$axios
      .get("/api/admin/posts/" + this.$route.params.id)
      .then((response) => {
        this.posts = response.data.data;
        this.imagePreview = response.data.data.image;
      });

    this.$axios.get("/api/admin/categories").then((response) => {
      this.categories = response.data.data.data;
    });

    this.$axios.get("/api/admin/tags").then((response) => {
      this.tags = response.data.data.data;
    });
  },

  methods: {
    onFileChange(event) {
      const file = event.target.files[0];
      this.posts.image = file;

      const reader = new FileReader();
      reader.onload = (e) => {
        this.imagePreview = e.target.result;
      };
      reader.readAsDataURL(file);
    },

    async updatePosts() {
      try {
        let tags = this.posts.tags;
        let selectedTags = [];

        tags.forEach((tag) => {
          selectedTags.push(tag.id);
        });

        const formData = new FormData();

        formData.append("image", this.posts.image);
        formData.append("title", this.posts.title);
        formData.append(
          "category_id",
          this.posts.category_id ? this.posts.category_id.id : ""
        );
        formData.append("content", this.posts.content);
        formData.append("description", this.posts.description);
        formData.append("_method", "PATCH");

        // append tags array
        for (let i = 0; i < selectedTags.length; i++) {
          formData.append("tags[]", selectedTags[i]);
        }

        const response = await this.$axios.post(
          "/api/admin/posts/" + this.$route.params.id,
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          }
        );

        console.log(response);

        // if (response.status === 200) {
        this.$swal.fire({
          title: "Berhasil",
          text: "Posts berhasil dirubah!",
          icon: "success",
          showCancelButton: false,
          timer: 2000,
        });

        // redirect, if success
        this.$router.push({ name: "admin-posts" });
        // }
      } catch (error) {
        console.log(error);

        if (error.response && error.response.status === 422) {
          this.validation = error.response.data;
        }
      }
    },
  },
};
</script>