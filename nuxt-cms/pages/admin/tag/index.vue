<template>
  <div class="content-wrapper mb-5">
    <section class="content-header">
      <div class="container-fluid"></div>
    </section>

    <section class="content">
      <div class="card card-outline card-info">
        <div class="card-header">
          <h3 class="card-title"><i class="nav-icon fas fa-tags"></i> TAGS</h3>
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
                  :to="{ name: 'admin-tag-create' }"
                  class="btn btn-info btn-sm"
                  style="padding-top: 8px"
                  ><i class="fa fa-plus-circle"></i> TAMBAH</nuxt-link
                >
              </div>
              <input
                type="text"
                class="form-control"
                placeholder="cari berdasarkan nama tag"
              />
              <div class="input-group-append">
                <button class="btn btn-info">
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
            :items="tags"
            :fields="fields"
            show-empty
          >
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
      title: "Tags",
      meta: [
        { name: "description", content: "Tags" },
        { name: "keywords", content: "tags, cms" },
      ],
    };
  },

  data() {
    return {
      // table header
      fields: [
        {
          label: "Nama Tag",
          key: "name",
        },
        {
          label: "Actions",
          key: "actions",
          tdClass: "text-center",
        },
      ],
    };
  },

  //   mengambil nilai dari URL yng memiliki parameter bernama page
  watchQuery: ["page"],

  async asyncData({ $axios, query }) {
    // page
    let page = query.page ? parseInt(query.page) : "";

    // fetching data tags
    const tags = await $axios.get(`/api/admin/tags?page=${page}`);
    return {
      tags: tags.data.data,
      pagination: tags.data,
    };
  },

  methods: {
    changePage(page) {
      this.$router.push({
        path: this.$route.path,
        query: {
          q: this.$route.query.q,
          page: page,
        },
      });
    },
  },
};
</script>