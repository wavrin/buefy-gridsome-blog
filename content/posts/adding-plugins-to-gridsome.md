---
title: Adding plugins to a Gridsome site
date: 2019-11-12
published: true
tags: ["Vue.js"]
canonical_url: false
description: "The Gridsome community has a pretty strong set of plugins. But you aren't limited to just those. Incorporating a Vue.js component into your site isn't too hard either."
---

[Gridsome](https://gridsome.org/) is a great static site generator, written in Vue.js, to host websites. I have [written](https://www.wavrin.com/new-site-with-gridsome-and-amplify/) before about why I chose Gridsome. I started this site with a great [starter template](https://github.com/gridsome/gridsome-starter-blog), but wanted to add a few more features to it. Thankfully, the Gridsome community has a number of quality plugins to make this easy.

There are three features I wanted:

* Google Analytics to track site visits
* A sitemap.xml to help crawlers index the site
* [Disqus](https://disqus.com/) comments on each blog post

 The first two - Google Analytics and a sitemap generator - have official [Gridsome plugins](https://gridsome.org/plugins/). But for Disqus comments I will have to do a tiny extra, using a Vue.js plugin instead. One quick note: I'm using *yarn* instead of *npm*, so that's what you'll see below. The plugin pages have instructions for npm if that's how you do things. :)
  
### Google Analytics
 
 The @gridsome/plugin-google-analytics [page](https://gridsome.org/plugins/@gridsome/plugin-google-analytics) makes this quite simple.
 
`yarn add @gridsome/plugin-google-analytics`
 
 This adds the library to your `packages.json` file. Now you just need to copy/paste the code into your `gridsome.config.js` file.
 
```
module.exports = {
  plugins: [
    {
      use: '@gridsome/plugin-google-analytics',
      options: {
        id: 'UA-XXXXXXXXX-X'
      }
    }
  ]
}
```

Put your actual tracking ID in there and you are set. `gridsome build` is all you need before deploying this.

### Sitemap

Following the instructions on the [@gridsome/plugin-sitemap page](https://gridsome.org/plugins/@gridsome/plugin-sitemap): `yarn add @gridsome/plugin-sitemap`

You do need to make sure that your `gridsome.config.js` has a `siteUrl` set. The plugin example has some different configuration options, depending on your pages and url structure. Mine is pretty simple, as all I have are blog posts for the top directory. So my plugin code for my `gridsome.config.js` is:

```
module.exports = {
  plugins: [
    {
      use: '@gridsome/plugin-sitemap',
      options: {
        cacheTime: 600000, // default
        exclude: ['/exclude-me'],
        config: {
          '/*': {
            changefreq: 'weekly',
            priority: 0.5
          }
        }
      }
    }
  ]
}
```

I left the "exclude" part in, even though I don't currently use it. After you build your site, your sitemap should be available at `/sitemap.xml` You may not see it locally, but `gridsome build` and deploy that, and you should see it in production.

I would suggest using [Google Webmasters](https://www.google.com/webmasters/) to show Google exactly where your sitemap is. It will then tell you if you have any errors.

### Disqus Comments

This one's just a bit tricker because there is not an official Gridsome plugin. You will have to use the [vue-disqus](https://github.com/ktquez/vue-disqus) component. Make sure to check the link for current instructions, but at this time:

`npm install vue-disqus` gets it into your `package.json` You will now need to install it in your Vue app. In your `main.js` you'll need to import it and then register it. Here is what my `main.js` looks like:

```
import '~/assets/style/index.scss'

import DefaultLayout from '~/layouts/Default.vue'

import VueDisqus from "vue-disqus";

export default function (Vue, { router, head, isClient }) {
  Vue.component('Layout', DefaultLayout);
  Vue.use(VueDisqus);
}
```

Finally, if you are going to use comments like I do - underneath each blog post - you would put the following code in your template:

```
  <div class="comments">
    <vue-disqus shortname="your_shortname_disqus" :identifier="page_id" url="http://example.com/path"></vue-disqus>
  </div>
```

Just add your short name from your Disqus account and the url of your site. And that's it.

Check all your work locally with `gridsome develop`. Once you confirm it all looks good, build again: `gridsome build` and deploy.

