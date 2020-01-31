---
title: Ruby on Rails development with Docker
date: 2019-03-04
published: true
tags: ["Docker", "Rails"]
canonical_url: false
description: "You may see an app you worked on months ago, but you don’t remember what it was about. Then you run into trouble trying to start it again because you have moved on to a new version of Rails. And probably a new version of Ruby too. One way to get around this version issue and save your sanity down the road: Use Docker."
---

There’s a terrific book called [Practicing Rails](https://www.justinweiss.com/practicing-rails/), by Justin Weiss. One of the key concepts that really stuck with me is creating many small apps to test and experiment with new features and ideas. There’s just one potential headache that you may encounter: you end up with, over time, a whole bunch of apps using different versions of [Ruby on Rails](https://rubyonrails.org/) and/or [Ruby](https://www.ruby-lang.org/).

You may see an app you worked on months ago, but you don’t remember what it was about. Then you run into trouble trying to start it again because you have moved on to a new version of Rails. And probably a new version of Ruby too. One way to get around this version issue and save your sanity down the road: Use [Docker](https://www.docker.com/).

By using Docker you can lock-in the versions and in the future more easily start your app up again.

In this short guide we’ll start a Rails app using containers.

We’ll need to install [Docker Desktop](https://www.docker.com/products/docker-desktop). Once we have Docker running locally, we can begin:

**Start bash in a new container:**

`docker run -it --rm -v ${PWD}:/usr/src/app ruby:2.4 bash`

This command starts up a container running Ruby and puts us right into it at a bash prompt. In this example, our container will be running Ruby 2.4, as the container was created using the official Ruby 2.4 image from [Docker Hub](https://hub.docker.com/_/ruby/). But we can specify whatever version you want.

If we don’t already have the Ruby 2.4 image locally, the first time you run this it takes some time. But it will then be cached locally, so next time it will go quite quickly.

Now, while we are in the container, we can install rails.

**Install Rails:**

`gem install rails`

And with that, we can create a rails app.

**Create rails app:**

```
cd /usr/src/app
rails new myapp --skip-test --skip-bundle --database=postgresql
```

As you may have noticed, we are using [PostgreSQL](https://www.postgresql.org/). In the past, if we were just creating one-off Rails projects to try out a new feature or experiment, we would probably just use the default [SQLite](https://www.sqlite.org/index.html); it can be a bit time-consuming to run postgres locally. But with Docker, it’s super easy. And if we ever further develop the application and want to push it to production we will be glad we did this.

So far we’ve been working _inside_ the container. Let’s get out of there.

**Exit the container:**

`exit`

Even though we created the Rails project while we were inside the container, you will notice that we can see the code after we exited. Our code stays locally and when we start a container it’s mounted inside.

**Change directory into your app:**

`cd myapp`

The Ruby image we used got us started. But we need to actually build the image - based on that same Ruby image - with everything we need to run our application. The nuts and bolts of this isn’t so important right now. But what we need is a Dockerfile.

**Create a Dockerfile**. Here is a good starter one:

```
FROM ruby:2.4
LABEL maintainer="your-email-goes-here"

# Ensure we install an up-to-date version of Node
# See https://github.com/yarnpkg/yarn/issues/2888
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash -

# Ensure latest packages for Yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | \
  tee /etc/apt/sources.list.d/yarn.list

# Install packages
RUN apt-get update -yqq && apt-get install -yqq --no-install-recommends \
  nodejs \
  yarn

COPY Gemfile* /usr/src/app/
WORKDIR /usr/src/app
RUN bundle install

COPY . /usr/src/app/

CMD ["bin/rails", "s", "-b", "0.0.0.0"]
```

And we’ll need to update our database configuration.

**Update database.yml**

```
default: &default
  adapter: postgresql
  encoding: unicode
  host: database
  username: postgres
  password: some-long-password
  database: myapp_development
  pool: 5
  variables:
    statement_timeout: 5000

development:
  <<: *default

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
```

Now we can build our docker image.

**Build a docker image:**

`docker build .`

Our application is actually made up of different services, so we need to describe them. We have our “web” service that consists of our running Rails application. And we’ll need a separate “database” service for the postgres database (as it’ll be running in its own container). This is where the docker-compose.yml file comes in.

**Create a docker-compose.yml file:**

```
version: "3"

services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/usr/src/app

  database:
    image: postgres
```

If you are curious why the database service is so simply stated, it’s because we don’t need to build anything. It is grabbing the official postgres image from Docker Hub. The image has everything it needs for us to launch a database container. Rails already knows the port.

To create our development database, we need to launch the database container first:

`docker-compose up -d database`

The -d flag allows the container to run in the background - “detached” mode, to be specific - so we don’t see any output.

Now, with postgres running, we can use Rails create the database:

`docker-compose run --rm web bin/rails db:create`

There are two parts to this command: a Docker part and a Rails part. The Docker part - `docker-compose run --rm web` - is launching our “web” container but will delete the container (the "--rm" part) as soon as the command runs. The rest of the command — `bin/rails db:create` - is the Rails command to create the database.

With the database created, we can now start up the web container.

**Start it up:**

`docker-compose up`

This command starts up all the services listed in your docker-compose.yml file.

That’s pretty much it. Our Rails app should be running locally at http://localhost:3000/

**Starting, Stopping, and other important commands**

When you want to get your services running:

`docker-compose up`

When you want to get your services running, but OK to run in the background:

`docker-compose up -d`

Check to see if you have any containers running:

`docker-compose ps`

When you want to stop your services:

`docker-compose stop`

Run a one-off rails command and delete the container after completion:

`docker-compose run --rm web bin/rails db:migrate`

**Epilogue**

We can now easily create lots of Rails apps. And in a year from now, when we ask ourselves, “What’s this app all about?” we won’t have to jump through any hoops to get it running again; our Ruby and Rails versions will all be nicely tucked away and we can easily launch a new container to run the app.

If we decide that one of our apps will go to production eventually, there are some advanced topics to consider:

- We’ll want to move your database information into environment variables.
- In Development, Rails compiles our assets on each request. But in Production, we’ll need to pre-compile the assets.
- We’d likely add more services, like Redis.
- If we are developing a lot on this application, we may grow tired of it running `bundle install` all the time. Gem caching is a great strategy to add.

If you are interested in these more advanced topics, I highly recommend Rob Isenberg’s [Docker for Rails Developers](https://pragprog.com/book/ridocker/docker-for-rails-developers).
