<template>
  <Layout>
    <PostCard v-for="edge in $page.posts.edges" :key="edge.node.id" :post="edge.node" />
  </Layout>
</template>

<page-query>
query {
  posts: allPost(filter: { published: { eq: true }}) {
    edges {
      node {
        id
        title
        date (format: "D. MMMM YYYY")
        timeToRead
        description
        path
      }
    }
  }
}
</page-query>

<script>
import PostCard from "~/components/PostCard.vue";

export default {
  metaInfo: {
    title: "Hello, world!"
  },
  components: {
    PostCard
  }
};
</script>

<style>
.home-links a {
  margin-right: 1rem;
}
</style>
