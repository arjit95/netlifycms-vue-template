<template>
  <v-container class="post-container">
    <v-img class="featured-image" :src="post.featured_image" />
    <h1 class="post-title mb-4 mt-4 text-h4">{{ post.title }}</h1>
    <v-row>
      <v-col cols="1">
        <v-avatar size="48px" class="pt-1">
          <v-img :src="post.author.profilePicture" />
        </v-avatar>
      </v-col>
      <v-col>
        <div class="text-subtitle-1 text-capitalize author-info">
          by
          <nuxt-link :to="'/author/' + post.author.slug">{{
            post.author.title
          }}</nuxt-link>
        </div>
        <div class="text-caption text-capitalize category-info">
          in
          <nuxt-link :to="'/category/' + post.category">{{
            post.category
          }}</nuxt-link>
        </div>
      </v-col>
    </v-row>
    <blockquote class="blockquote mb-3">{{ post.description }}</blockquote>
    <nuxt-content :document="post" />
  </v-container>
</template>

<style scoped>
.featured-image {
  max-height: 300px;
  background-size: cover;
}

.author-info a,
.category-info a {
  text-decoration: none;
}

.post-container {
  max-width: 700px;
}
</style>
<script>
export default {
  async asyncData({ $content, params, error }) {
    let post, author
    try {
      post = await $content('blog', params.slug).fetch()
      author = await $content('authors')
        .limit(1)
        .where({
          title: post.author,
        })
        .fetch()

      post.author = author[0]
    } catch (e) {
      error({ message: 'Blog Post not found' })
    }

    return {
      post,
    }
  },
  head() {
    return {
      title: this.post.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.post.description,
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: this.post.tags,
        },
        {
          hid: 'author',
          name: 'author',
          content: this.post.author.title,
        },
      ],
    }
  },
}
</script>
