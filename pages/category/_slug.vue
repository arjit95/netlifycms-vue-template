<template>
  <posts-list :posts="posts" :get-posts="getPosts"></posts-list>
</template>

<script>
import PostsList from '~/components/PostsList'

async function getMorePostsForCategory(category, page, limit, $content) {
  const start = page * limit

  let posts = await $content('blog')
    .sortBy('date')
    .skip(start)
    .limit(limit)
    .where({ category })
    .fetch()

  const authorNames = posts.map(({ author }) => author)

  let authors = await $content('authors')
    .where({ title: { $in: authorNames } })
    .fetch()

  authors = authors.reduce((acc, author) => {
    acc[author.title] = author
    return acc
  }, {})

  posts = posts.map((post) => {
    post.author = authors[post.author]
    return post
  })

  return { posts }
}

export default {
  components: { PostsList },
  asyncData({ $content, params }) {
    return getMorePostsForCategory(params.slug, 0, 10, $content)
  },
  data: () => {
    return {
      postsPerPage: 10,
    }
  },
  methods: {
    getPosts(page) {
      return getMorePostsForCategory(
        this.$route.params.slug,
        page,
        this.postsPerPage,
        this.$content
      )
    },
  },
}
</script>
