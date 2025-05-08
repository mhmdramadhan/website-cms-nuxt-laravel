<template>
  <div class="content-wrapper">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <section class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title">
            <i class="nav-icon fas fa-tags"></i> TAMBAH USER
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
          <form @submit.prevent="storeUser">
            <div class="form-group">
              <label>NAMA USER</label>
              <input
                type="text"
                v-model="user.name"
                placeholder="Masukkan Nama User"
                class="form-control"
              />
              <div v-if="validation.name" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.name[0]
                }}</b-alert>
              </div>
            </div>
            <div class="form-group">
              <label>EMAIL</label>
              <input
                type="email"
                v-model="user.email"
                placeholder="Masukkan Email"
                class="form-control"
              />
              <div v-if="validation.email" class="mt-2">
                <b-alert show variant="danger">{{
                  validation.name[0]
                }}</b-alert>
              </div>
            </div>
            <div class="form-group">
              <label>PASSWORD</label>
              <input
                type="password"
                v-model="user.password"
                placeholder="Masukkan Password"
                class="form-control"
              />
              <div v-if="validation.password" class="mt-2">
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
      title: "Tambah User",
      meta: [
        { name: "description", content: "Tambah User" },
        { name: "keywords", content: "User, Tambah User" },
      ],
    };
  },

  data() {
    return {
      user: {
        name: "",
        email: "",
        password: "",
      },
      validation: [],
    };
  },

  methods: {
    async storeUser() {
      try {
        const formData = new FormData();
        formData.append("name", this.user.name);
        formData.append("email", this.user.email);
        formData.append("password", this.user.password);

        const response = await this.$axios.post(
          "/api/admin/users",
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
          text: "User berhasil ditambahkan",
          icon: "success",
          showCancelButton: false,
          timer: 2000,
        });

        // redirect, if success
        this.$router.push({ name: "admin-user" });
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