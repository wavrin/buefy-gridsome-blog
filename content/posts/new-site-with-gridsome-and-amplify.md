---
title: New site with Gridsome and Amplify
date: 2019-11-10
published: true
tags: ["Vue.js"]
canonical_url: false
description: "I've lost count how many iterations this website has had. So, of course, I wanted to build another one."
---

I've lost count how many iterations this website has had. There was a simple HTML/CSS site; at least twice powered by Wordpress, once by Drupal. Then there was the Ruby on Rails version, and even one using Django. There was the shared hosting, the Digital Ocean droplet, and the experiments with various services at AWS. I can say, with certainty, that I enjoyed building each version.

If I had always been focused on the content, then the smart thing would have been to just go with Wordpress and stick with it. But I've used this site as a playground to try new technologies. And so here we are again...

This time around, there were two things I wanted to work with: Vue.js and some sort of hosting experience where I could serve static pages, without having to maintain a backend. This is where [JAMstack](https://jamstack.org/) comes in.

- **J**avascript: It will handle requests/responses from within the client.
- **A**PIs: Any data requests use reusable APIs.
- **M**arkdown: You write your content in markdown files, which will be pre-built at runtime.

JAMstack encompasses several approaches. What I was really interested in is a static site generator. And it happens that many static site generators are considered JAMstack-worthy.

In the Vue.js community there are a few options. Among them, [Vuepress](https://vuepress.vuejs.org/) and [Gridsome](https://gridsome.org/). I chose Gridsome because its documentation is amazing, it has a large plugin community, and many starter templates.

With Gridsome installed locally, you can develop your site and build it - so it's ready to be deployed. To host my site, I used [AWS Amplify](https://aws.amazon.com/amplify/). Amplify is then pointed to my GitHub repository and as soon as I update it (like adding a new blog post), Amplify grabs the updated build and deploys.

Is this the way of the future? Well, I'm not sure. But it's pretty slick for now. I have no server I need to maintain and pages are delivered blazingly fast. I'm writing my posts in Markdown, but not everyone's interested in doing that. There are other options, such as headless CMSs.
