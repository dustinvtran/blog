# Blog

This blog is built with [Jekyll](https://jekyllrb.com/).

The theme builds off
[Type Theme](https://rohanchandra.github.io/project/type/), and is
heavily inspired by [Otoro](http://blog.otoro.net/), the [New York
Times](http://www.nytimes.com/), and the
[Rosenrot](http://the-rosenrot.com/). The annals-like frontpage takes
cue from Paul Graham's [essays](http://paulgraham.com/articles.html)
and Cosma Shalizi's [notebooks](http://bactra.org/notebooks/).

The following Jekyll plugins are used:

+ [KaTeX](https://khan.github.io/KaTeX/), Google Fonts, Google Analytics, Normalize, Pygments (comes with TypeTheme)
+ [Jekyll Scholar](https://github.com/inukshuk/jekyll-scholar)

## Workflow

Here's my workflow for writing and submitting blog posts.

1. Dump thoughts into a markdown file, in `_drafts/`. Or edit the many
   files already inside `_drafts/`. Preview (and generate) the static
   site from a local server.

  ```bash
  jekyll serve --drafts
  ```
2. When complete, rename and move the file to `_posts/`.
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
6. Re-build the site. TODO

  ```bash
  jekyll build
  ```

## Maintenance

To keep the theme up to date, I track the theme's original repo on
the `type-theme` branch. Add to remote the original repo,
```
git remote add theme git@github.com:rohanchandra/type-theme.git
```
Whenever you want to update, simply run
```
git checkout type-theme
git pull theme master
```
You can compare `type-theme` to `master` and possibly merge in any
changes. Keeping the theme up-to-date on a separate branch avoids
treating the repo as a fork: this repo does more than just style
things and is thus not appropriate as a fork.
