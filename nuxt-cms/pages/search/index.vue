<template>
  <b-container class="mt-5 mb-5">
    <b-row>
      <b-col md="12" class="mb-3">
        <h4>
          PENCARIAN DENGAN KATA KUNCI : <strong>{{ $route.query.q }}</strong>
        </h4>
      </b-col>
      <b-col md="4" class="mb-3" sm="12" v-for="post in posts" :key="post.id">
        <b-card
          :img-src="post.image"
          img-top
          tag="article"
          class="mb-2 h-100 rounded-lg"
        >
          <div class="mb-2">
            <small class="text-muted"
              ><i class="fa fa-calendar"></i> {{ post.created_at }}</small
            >
          </div>
          <h5>
            <nuxt-link
              :to="{ name: 'post-slug', params: { slug: post.slug } }"
              >{{ post.title }}</nuxt-link
            >
          </h5>
          <b-card-text> {{ post.description.substr(0, 55) }}... </b-card-text>
          <b-card-text>
            <small class="text-muted"
              ><i class="fa fa-comments"></i>
              {{ post.comments.length }} Komentar</small
            >
          </b-card-text>
        </b-card>
      </b-col>
    </b-row>
    <b-row class="mt-4 justify-content-center">
      <b-pagination
        v-model="pagination.current_page"
        :total-rows="pagination.total"
        :per-page="pagination.per_page"
        @input="changePage"
        aria-controls="my-table"
      >
      </b-pagination>
    </b-row>
  </b-container>
</template>
  
  <script>
export default {
  //meta
  head() {
    return {
      title:
        "Search - SantriKoding.com - Belajar Koding Bahasa Indonesia Terlengkap",
      meta: [
        {
          hid: "og:title",
          name: "og:title",
          content:
            "SantriKoding.com - Belajar Koding Bahasa Indonesia Terlengkap",
        },
        {
          hid: "og:site_name",
          name: "og:site_name",
          content:
            "SantriKoding.com - Belajar Koding Bahasa Indonesia Terlengkap",
        },
        {
          hid: "og:image",
          name: "og:image",
          content:
            "https://cdn.jsdelivr.net/gh/maulayyacyber/assets-images-ebooks/cms-nuxt-laravel-11/xKOCz0P.png",
        },
      ],
    };
  },

  //watch query URL
  watchQuery: ["q", "page"],

  async asyncData({ $axios, query }) {
    //fetching posts
    const posts = await $axios.$get(
      `/api/web/posts?q=${query.q}&page=${parseInt(query.page)}`
    );

    return {
      posts: posts.data.data,
      pagination: posts.data,
    };
  },

  methods: {
    //change page pagination
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
  
  <style>
</style>