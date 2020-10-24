# jeykll blog from scratch

This is a WIP jekyll blog with (no/a custom) theme.

The result can be seen at: tdb.

Some of the creation process is itself written down as blog posts at: tbd.

Inspiration came mostly from the [minima]() and the [chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) themes.

## Development

Locally, just clone the repository and

```bash
bundle
bundle exec jekyll serve
```

### Updates

```bash
bundle update
```

Because the external assets are hand-picked, these have to be checked and
included separately, namely:

`css/fork-awesome-1.1.7` [source](https://github.com/ForkAwesome/Fork-Awesome/releases)

## Features

Some features come from jekyll-plugins:

* haml syntax
* Table of contents

Others are implemented via layouts and Co. or inherent:

* works without JS
* self-contained (no external resources)

## Architecture doc

* layout
  * learn grid and flex
* use toc

## Mentions

jekyll, chirpy, normalize, fork awesome

## License

If not stated otherwise in the files itself or listed here, the code is
published under the [AGPLv3+](), Copyright 2020 Felix Wolfsteller. The content
is published as [](), Copyright 2020 Felix Wolfsteller .
