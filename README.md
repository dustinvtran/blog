# Blog

This blog is built with [Jekyll](https://jekyllrb.com/).

The theme is based off [Type Theme](https://rohanchandra.github.io/project/type/),
with heavy inspiration by [Otoro](http://blog.otoro.net/). The
frontpage is inspired by Paul Graham's minimal
[essays](http://paulgraham.com/articles.html) page, made slightly more
polished and modern.

The following Jekyll plugins are used:

+ KaTeX (comes with TypeTheme)
+ RSS (comes with TypeTheme)
+ Jekyll Scholar

## Workflow

Here's my workflow for writing and submitting blog posts.

1. Dump thoughts into a markdown file, in `_drafts/`. Or edit the many
   files already inside `_drafts/`. Preview it with
  TODO

  ```bash
  ```
  View (and generate) the static site on a local server.
  ```bash
  jekyll serve
  ```
2. When complete, rename and move markdown file to `_posts/`.
3. Push file to github.

  ```bash
  git add ...
  git push origin master
  ```
4. SSH into VPS.

  ```bash
  sshrc digitalocean
  ```
5. Pull branch changes from github.

  ```bash
  cd /var/www/dustintran.com/blog
  git pull origin master
  ```
6. Re-build the site.

  ```bash
  jekyll build
  ```
