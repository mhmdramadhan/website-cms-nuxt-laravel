<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <div class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> TAMBAH TAG
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
          <form @submit.prevent="storeTag">
            <div class="form-group">
              <label>NAMA TAG</label>
              <input
                type="text"
                v-model="tag"
                placeholder="Masukkan Nama Tag"
                class="form-control"
              />
              <div v-if="validation.name" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.name[0]
                }}</b-alert>
              </div>
            </div>

            <button class="btn btn-info mr-1 btn-submit" type="submit">
              <i class="fa fa-paper-plane"></i> SIMPAN
            </button>
            <button class="btn btn-warning btn-reset" type="reset">
              <i class="fa fa-redo"></i> RESET
            </button>
          </form>
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
      title: "Tambah Tag",
      meta: [
        { name: "description", content: "Tambah Tag" },
        { name: "keywords", content: "Tag, Tambah Tag" },
      ],
    };
  },

  data() {
    return {
      tag: "",
      validation: [],
    };
  },

  methods: {
    async storeTag() {
      // send data to server
      await this.$axios
        .post("/api/admin/tags", {
          // data
          name: this.tag,
        })

        .then(() => {
          // sweet alert
          this.$swal.fire({
            title: "Berhasil",
            text: "Tag berhasil ditambahkan",
            icon: "success",
            showCancelButton: false,
            timer: 2000,
          });

          // redirect, if success
          this.$router.push({
            name: "admin-tag",
          });
        })
        .catch((error) => {
          // if error
          if (error.response.status === 422) {
            // validation error
            this.validation = error.response.data;
          } else {
            // other error
            this.$swal.fire({
              title: "Gagal",
              text: "Tag gagal ditambahkan",
              icon: "error",
              showCancelButton: false,
              timer: 2000,
            });
          }
        });
    },
  },
};
</script>