<template>
  <posts-list
    :posts="posts"
    :authors="authors"
    :get-posts="getPosts"
  ></posts-list>
</template>

<script>
import PostsList from '~/components/PostsList'

async function getPostsFromAuthor(author, page, limit, $content) {
  const authorInfo = await $content('authors', author).fetch()

  const start = page * limit
  let posts = await $content('blog')
    .skip(start)
    .limit(limit)
    .where({ author: authorInfo.title })
    .fetch()

  posts = posts.map((post) => {
    post.author = authorInfo
    return post
  })

  return { posts }
}

export default {
  components: { PostsList },
  asyncData({ $content, params }) {
    return getPostsFromAuthor(params.slug, 0, 10, $content)
  },
  data: () => {
    return {
      postsPerPage: 10,
    }
  },
  methods: {
    getPosts(page) {
      return getPostsFromAuthor(
        this.$route.params.slug,
        page,
        this.postsPerPage,
        this.$content
      )
    },
  },
}
</script>
