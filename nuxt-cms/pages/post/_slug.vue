<template>
    <b-container class="mt-5 mb-5">
      <b-row>
        <b-col md="8" sm="12">
          <b-card class="border-0 rounded-lg shadow-md mb-3">
            <b-img :src="post.image" class="rounded-lg w-100"></b-img>
            <hr>
            <h4>{{ post.title }}</h4>
            <b-card-text>
              <small class="text-muted mr-3"><i class="fa fa-calendar"></i> {{ post.created_at }} </small>
              <small class="text-muted mr-3"><i class="fa fa-user"></i> {{ post.user.name }}</small>
              <small class="text-muted mr-3"><i class="fa fa-folder"></i> {{ post.category.name }}</small>
              <small class="text-muted"><i class="fa fa-comments"></i> {{ post.comments.length }} Komentar</small>
            </b-card-text>
            <div v-html="post.content"></div>
            <hr>
            <nuxt-link :to="{name: 'tag-slug', params:{slug: tag.slug}}" class="btn btn-primary btn-sm mb-2 mr-2 shadow-sm" v-for="tag in post.tags" :key="tag.id">
              {{ tag.name }}</nuxt-link>
          </b-card>
  
          <b-card no-body>
            <b-card-body>
              <h5> {{ post.comments.length }} KOMENTAR</h5>
              <hr>
              <b-list-group>
                <b-list-group-item v-for="comment in post.comments" :key="comment.id">
                  <div class="text-right">
                    <small class="text-right text-muted">{{ comment.created_at }}</small>
                  </div>
                  <b-avatar button variant="primary" text="" class="align-baseline"></b-avatar>
                  <span class="font-weight-bold">{{ comment.name }}</span>
                  <hr>
                  {{ comment.comment }}
                </b-list-group-item>
              </b-list-group>
            </b-card-body>
          </b-card>
  
          <b-card no-body>
            <b-card-body>
              <h5>KIRIM KOMENTAR</h5>
              <hr>
              <b-form @submit.prevent="storeComment">
                <b-row>
                  <b-col md="6" sm="6">
                    <div class="form-group">
                      <label class="font-weight-bold text-uppercase">Nama Lengkap</label>
                      <input type="text" v-model="comment.name" class="form-control" placeholder="Masukkan Nama Lengkap">
                      <div v-if="validation.name" class="mt-2">
                        <b-alert show variant="danger">{{ validation.name[0] }}</b-alert>
                      </div>
                    </div>
                  </b-col>
                  <b-col md="6" sm="6">
                    <div class="form-group">
                      <label class="font-weight-bold text-uppercase">Alamat Email</label>
                      <input type="email" v-model="comment.email" class="form-control" placeholder="Masukkan Alamat Email">
                      <div v-if="validation.email" class="mt-2">
                        <b-alert show variant="danger">{{ validation.name[0] }}</b-alert>
                      </div>
                    </div>
                  </b-col>
                  <b-col md="12">
                    <div class="form-group">
                      <label class="font-weight-bold text-uppercase">Komentar</label>
                      <textarea class="form-control" v-model="comment.comment" rows="4" placeholder="Masukkan Komentar"></textarea>
                      <div v-if="validation.comment" class="mt-2">
                        <b-alert show variant="danger">{{ validation.comment[0] }}</b-alert>
                      </div>
                    </div>
                  </b-col>
                  <b-col md="12">
                    <b-button variant="primary" type="submit">KIRIM</b-button>
                    <b-button variant="warning" type="reset">RESET</b-button>
                  </b-col>
                </b-row>
              </b-form>
            </b-card-body>
          </b-card>
  
        </b-col>
  
        <b-col md="4" sm="12">
          <b-card no-body class="border-0 rounded-lg shadow-md">
            <b-card-body>
              <h5>KATEGORI</h5>
              <hr>
              <b-card no-body class="border-0 shadow-sm card-category mb-2 rounded-lg" v-for="category in categories"
                :key="category.id">
                <b-card-body class="p-2 fw-normal">
                  <b-img :src="category.image" width="40"></b-img>
                  <nuxt-link :to="{name: 'category-slug', params: {slug: category.slug}}" class="text-dark">{{ category.name }}</nuxt-link>
                </b-card-body>
              </b-card>
            </b-card-body>
          </b-card>
  
          <b-card no-body class="border-0 rounded-lg shadow-md">
            <b-card-body>
              <h5>TAGS</h5>
              <hr>
              <nuxt-link :to="{name: 'tag-slug', params:{slug: tag.slug}}" class="btn btn-primary btn-sm mb-2 mr-2 shadow-sm" v-for="tag in tags" :key="tag.id">{{ tag.name }}</nuxt-link>
            </b-card-body>
          </b-card>
  
        </b-col>
      </b-row>
    </b-container>
  </template>
  
  <script>
    export default {
  
      //meta
      head() {
        return {
          title: this.post.title,
          meta: [
            {
              hid: 'og:title',
              name: 'og:title',
              content: this.post.title
            },
            {
              hid: 'og:site_name',
              name: 'og:site_name',
              content: this.post.title
            },
            {
              hid: 'og:image',
              name: 'og:image',
              content: this.post.image
            },
            {
              hid: 'description',
              name: 'description',
              content: this.post.description
            },
          ]
        }
      },
  
      async asyncData({ params, $axios }) {
  
        //fetching post
        const post = await $axios.$get(`/api/web/posts/${params.slug}`)
  
        //fetching categories
        const categories = await $axios.$get('/api/web/categorySidebar')
  
        //fetching tags
        const tags = await $axios.$get('/api/web/tags')
  
        return {
          'post': post.data,
          'categories': categories.data,
          'tags': tags.data
        }
      },
  
      /**
      * kirim komentar
      */
      data() {
          return {
              //state comment
              comment: {
                name: '',
                email: '',
                comment: ''
              },
              //state validation
              validation: []
          }
      },
  
      methods: {
  
        //metthod storeComment
        async storeComment() {
  
          //sending data to server
          await this.$axios.post('/api/web/posts/storeComment', {
  
            //data
            name: this.comment.name,
            email: this.comment.email,
            comment: this.comment.comment,
            slug: this.$route.params.slug
          })
          .then(() => {
  
            //feresh data
            this.$nuxt.refresh()
  
            //clear form
            this.comment.name = ''
            this.comment.email = ''
            this.comment.comment = ''
  
            //show sweet alert
            this.$swal.fire({
              title: 'BERHASIL!',
              text: "Komentar Anda Berhasil Terkirim!",
              icon: 'success',
              showConfirmButton: false,
              timer: 3000
            })
  
            this.$router.push({ path: this.$route.path });
  
          })
          .catch(error => {
  
            //assign error validation
            this.validation = error.response.data
  
          })
        }
  
      }
  
    }
  </script>
  
  <style>
  
  </style>