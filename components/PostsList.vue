<template>
  <v-container>
    <v-card
      v-for="post of posts"
      :key="post.slug"
      class="mx-auto pb-2"
      max-width="600"
    >
      <v-img
        class="white--text align-end"
        height="200px"
        :src="post.featured_image"
      >
      </v-img>
      <v-row class="ms-1">
        <v-col cols="1">
          <v-avatar size="48px" class="pt-1">
            <v-img :src="post.author.profilePicture" />
          </v-avatar>
        </v-col>
        <v-col class="ms-2">
          <div class="text-subtitle-1 text-capitalize">
            by
            <nuxt-link :to="'/author/' + post.author.slug">{{
              post.author.title
            }}</nuxt-link>
          </div>
          <div class="text-caption text-capitalize">
            in
            <nuxt-link :to="'/category/' + post.category">{{
              post.category
            }}</nuxt-link>
          </div>
        </v-col>
      </v-row>
      <v-card-subtitle class="pb-0"
        >{{ humanizeDate(post.date) }} ago</v-card-subtitle
      >
      <v-card-title class="pt-1">{{ post.title }}</v-card-title>
      <v-card-text class="text--primary">
        {{ post.description }}
      </v-card-text>

      <v-card-actions>
        <NuxtLink :to="'/blog/' + post.slug">
          <v-btn color="primary" link> Read </v-btn>
        </NuxtLink>
      </v-card-actions>
    </v-card>
  </v-container>
</template>
<style scoped>
a {
  text-decoration: none;
}
</style>
<script>
import {
  HumanizeDurationLanguage,
  HumanizeDuration,
} from 'humanize-duration-ts'

const hdl = new HumanizeDurationLanguage('en')
const hd = new HumanizeDuration(hdl)

export default {
  props: {
    posts: {
      type: Array,
      default: () => [],
    },
    getPosts: {
      type: Function,
      default: () => ({ posts: [] }),
    },
  },
  data: () => {
    return {
      isLoading: false,
      shouldLoad: true,
      page: 1,
    }
  },
  mounted() {
    this.$nextTick(() => {
      window.addEventListener('scroll', this.onScroll)
    })
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.onScroll)
  },
  methods: {
    onScroll() {
      if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
        this.loadAdditionalPosts()
      }
    },

    async loadAdditionalPosts() {
      if (this.isLoading || !this.shouldLoad) {
        return
      }

      this.isLoading = true
      const response = await this.getPosts(++this.page)
      if (!response.posts.length) {
        this.shouldLoad = false
        this.isLoading = false

        return
      }

      this.posts = this.posts.concat(response.posts)
    },

    humanizeDate(dateString) {
      const date = new Date(dateString)
      return hd.humanize(Date.now() - date.getTime(), { largest: 1 })
    },
  },
}
</script>
