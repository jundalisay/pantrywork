+++
title = "Deploy Phoenix 1.6.2 to Gigalixir and Heroku"
subtitle = ""
banner = "/img/tech.jpg"
description = "We list how to deploy Phoenix 1.6.2 to Gigalixir and Heroku"
tags = ['Phoenix', 'Gigalixir', 'Heroku']
date = 2021-12-11
+++

We switched from Python Flask and Ruby on Rails to Phoenix when we started getting users in our web app. 

This strained our server which either had to burst (leading to more charges) or restart. 

The dilemma was to go with Golang or Phoenix. Since Phoenix is heavily influenced by Rails, we naturally decided to go for Phoenix. 

Unlike Flask or Rails that needs a server with minimum 1GB RAM or more, Phoenix can run a full web app with 300 MB! This is in addition to its speed, and its nice LiveView feature, which turns Phoenix into a websocket server.  

The easiest way to deploy Phoenix is through Gigalixir or Heroku, so here are the steps for Phoenix 1.6.2, taken from the official guide.  This assumes you already have the Gigalixir and Heroku CLIs and web accounts. Let's say the app name is `socrates`.

## Heroku 

1 -- On Heroku Dashboard, create an app named `socrates`

2 -- On Heroku Dashboard, Go to Settings -> Buildpacks then add `hashnuke/elixir` and `https://github.com/gjaldon/heroku-buildpack-phoenix-static.git`

3 -- In your app's root directory, create the git remote 

```
heroku git:remote -a socrates
```

4 -- In your app's root directory, create the config files

```
$ echo 'elixir_version=1.12.2' > elixir_buildpack.config
$ echo 'erlang_version=24.0.3' >> elixir_buildpack.config
$ echo 'node_version=10.20.1' > phoenix_static_buildpack.config
```

5 -- In your `assets/package.json`

```
{
  ...
  "scripts": {
    "deploy": "cd .. && mix assets.deploy && rm -f _build/esbuild"
  }
  ...
}
```

6 -- In your `config/prod.exs`, replace the `url:` line with

```
url: [scheme: "https", host: "socrates.herokuapp.com", port: 443],
force_ssl: [rewrite_on: [:x_forwarded_proto]],
```

7 -- In your `config/runtime.xs` uncomment `ssl: true` 

```
ssl: true
```

8 -- In ` lib/socrates_web/endpoint.ex` add the following in the `socket` line

```
websocket: [timeout: 45_000]
```

9 -- Generate secret key
```
mix phx.gen.secret
```

10 -- Set the resulting key onto Heroku
```
heroku config:set SECRET_KEY_BASE="longsecretkey"
```

11 -- Commit to git and push to Heroku

```
git push heroku master
```

12 -- Create database after the app has been deployed
```
heroku addons:create heroku-postgresql:hobby-dev
heroku config:set POOL_SIZE=18
heroku run "POOL_SIZE=2 mix ecto.migrate"
heroku run "POOL_SIZE=2 mix run priv/repo/seeds.exs"
```

## Gigalixir 

1 -- Create an app through the terminal

```
gigalixir create -n socrates
```

2 -- Create the config files
```
$ echo 'elixir_version=1.10.3' > elixir_buildpack.config
$ echo 'erlang_version=22.3' >> elixir_buildpack.config
$ echo 'node_version=12.16.3' > phoenix_static_buildpack.config
```

3 -- In your `assets/package.json`
```
{
  ...
  "scripts": {
    "deploy": "cd .. && mix assets.deploy && rm -f _build/esbuild"
  }
  ...
}
```

4 -- Deploy
```
git push gigalixir 
```

5 -- Create, migrate, and seed the database

```
gigalixir pg:create --free
gigalixir run mix ecto.migrate
gigalixir run mix run priv/repo/seeds.exs
```

6 -- To reset the database, go to the Gigilixir dashboard, scale down the app, delete the database, then scale the app back to 1, then do step 5 
