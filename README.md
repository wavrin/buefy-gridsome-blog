# Simple blog using Gridsome and Buefy

## Install

### Gridsome CLI:

`npm install --global @gridsome/cli`

### Use this project as your starter:

1. `gridsome create my-site https://github.com/wavrin/buefy-gridsome-blog.git`
2. `cd my-site`
3. `gridsome develop`

## Customize

### Google Analytics

In your `gridsome.config.js` files, add your own Google Analytics id:

```
  {
    use: "@gridsome/plugin-google-analytics",
    options: {
      id: "UA-XXXXXXXXXXX"
    }
```

### Discus (for comments)

In your `post.vue` template, add your own Disqus shortname:

```
  <div class="post-comments">
    <vue-disqus shortname="yourshortname" :identifier="$page.post.title"></vue-disqus>
  </div>
```

### Font

The site is using a Google Font and is imported here: `/src/assets/style/_typography.scss`

`@import url("https://fonts.googleapis.com/css?family=Lato:400, 600");`

You can find a new font [here](https://fonts.google.com/) and update that file.

### Icons

The Author's card `AuthorCard.vue` uses icons from [Font Awesome](https://fontawesome.com/icons).

```
  <a class="icon" href="#">
    <i class="fab fa-twitter fa-2x"></i>
  </a>
```
