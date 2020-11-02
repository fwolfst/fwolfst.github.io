# jeykll blog from scratch for fwolfst.github.io

This is a WIP jekyll blog with (no/a custom) theme.

The result can be seen at: https://fwolfst.github.io.

Some of the creation process is itself written down as blog posts at: tbd.

Inspiration came mostly from the [minima]() and the [chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) themes.

## Development

Locally, just clone the repository and

```bash
bundle
bundle exec jekyll serve --livereload
```

Or to show drafts (including those with future dates), too

```bash
jekyll serve -D --future --livereload
```

and visit

    [http://localhost:4000/](http://localhost:4000)

### Updates

```bash
bundle update
```

Because the external assets are hand-picked, these have to be checked and
included separately, namely:

`css/fork-awesome-1.1.7` [source](https://github.com/ForkAwesome/Fork-Awesome/releases)
`css/normalize.css` [source](https://github.com/necolas/normalize.css)

## Features

Some features come from jekyll-plugins:

* haml syntax
* Table of contents
* very basic seo

Others are implemented via layouts and co. or inherent:

* works without JS
* self-contained (no external resources), no extra tracking

## Architecture doc

Pretty standard jekyll stuff afaik.

## Mentions

jekyll, chirpy, normalize, fork awesome

## Deployment

I use Jekyll github deploy: https://github.com/yegor256/jekyll-github-deploy .
See also https://fwolfst.github.io/jekyll/snack/2020/10/30/jgd.html .

## License

If not stated otherwise in the files itself or listed here, the code is
published under the [AGPLv3+](), Copyright 2020 Felix Wolfsteller. The content
is published as [CC-BY-NC-SA 4.0+](https://creativecommons.org/licenses/by-nc-sa/4.0), Copyright 2020 Felix Wolfsteller .
