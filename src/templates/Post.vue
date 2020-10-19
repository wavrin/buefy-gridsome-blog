<template>
  <Layout>
    <a class="icon" href="/">
      <i class="fa fa-arrow-left fa-2x"></i>
    </a>
    <h1 class="title">{{ $page.post.title }}</h1>
    <em>{{ $page.post.date }}</em>
    <div class="content" v-html="$page.post.content" />

    <div class="post-comments">
      <vue-disqus
        shortname="DISQUSSHORTNAME"
        :identifier="$page.post.title"
      ></vue-disqus>
    </div>
  </Layout>
</template>

<script>
import AuthorCard from "~/components/AuthorCard.vue";
import Footer from "~/components/Footer.vue";
export default {
  components: {
    AuthorCard,
    Footer,
  },
  metaInfo() {
    return {
      title: this.$page.post.title,
      meta: [
        {
          name: "description",
          content: this.$page.post.description,
        },
      ],
    };
  },
};
</script>

<page-query>
query Post ($id: ID!) {
  post: post (id: $id) {
    title
    path
    date (format: "MMMM D, YYYY")
    description
    content
  }
}
</page-query>

<style></style>
