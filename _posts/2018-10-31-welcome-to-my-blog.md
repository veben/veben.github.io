---
title: "Welcome to my blog"
date: 2018-10-31
tags:
  - ruby
  - jekyll
  - github-pages
---

I'm excited to share that I've just created my blog and published my first post. You can start your own blog too! Here’s a quick guide to get you started.

## Getting Started
You can easily create your own blog by cloning the [Starter repository](https://github.com/barryclark/jekyll-now) and customizing it to fit your needs. Most of the configurations are managed in the `_config.yml` file.
To add a new post, simply create a markdown file in the `_posts` directory that  following this naming convention: `YYYY-MM-DD-name-of-post.md`.

## Local Developement
Before publishing your blog, you might want to test it locally. Follow these steps to configure local environment:
1. Clone the repository
```sh
git clone https://github.com/<your-github-username>/<your-github-username>.github.io.git
```
2. Install Ruby if not alreday done
3. Install Github pages
```sh
gem install github-pages
```
4. Serve the site and watch for markup/sass changes
```sh
bundle exec jekyll serve
```
5. Open your browser and navigate to http://127.0.0.1:4000/ to view your site locally