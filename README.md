# Default starter for Gridsome

### 1. Install Gridsome CLI tool if you don't have

`npm install --global @gridsome/cli`

### 2. Create a Gridsome project

2. `cd my-gridsome-site` to open the folder
3. `gridsome develop` to start a local dev server at `http://localhost:8080`

Discus info

In your `post.vue` template, add your own Disqus shortname:

```
  <div class="post-comments">
    <vue-disqus shortname="yourshortname" :identifier="$page.post.title"></vue-disqus>
  </div>
```

Google Analytics

In your `gridsome.config.js` files, add your own Google Analytics id:

```
  {
    use: "@gridsome/plugin-google-analytics",
    options: {
      id: "UA-XXXXXXXXXXX"
    }
```

Font changes:

The site is using a Google Font and is imported here: `/src/assets/style/_typography.scss`

```
  @import url("https://fonts.googleapis.com/css?family=Lato:400, 600");
```

You can find a new font https://fonts.google.com/ change the font there if you like.

Author's card `AuthorCard.vue` uses icons from https://fontawesome.com/icons

```
  <a class="icon" href="#">
    <i class="fab fa-twitter fa-2x"></i>
  </a>
```
