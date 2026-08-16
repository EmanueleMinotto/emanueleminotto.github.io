# emanueleminotto.github.io

Source for [Emanuele Minotto's blog](https://emanueleminotto.github.io), built with [Jekyll](https://jekyllrb.com/).

## Prerequisites

- [Ruby](https://www.ruby-lang.org/en/downloads/) (see [Jekyll's requirements](https://jekyllrb.com/docs/installation/) for supported versions)
- [Bundler](https://bundler.io/) (`gem install bundler`)

## Running locally

1. Install dependencies:

   ```sh
   bundle install
   ```

2. Start the local server:

   ```sh
   bundle exec jekyll serve
   ```

3. Open [http://localhost:4000](http://localhost:4000) in your browser.

The server watches for file changes and rebuilds automatically. Draft posts and incremental builds can be enabled with:

```sh
bundle exec jekyll serve --drafts --incremental
```

## Writing posts

New posts go in [_posts/](_posts/), named `YYYY-MM-DD-title.markdown`. Front matter follows Jekyll's standard format (`layout`, `title`, `date`, etc.).
