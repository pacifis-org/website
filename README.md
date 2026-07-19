# Chirpy Starter [![Gem Version](https://img.shields.io/gem/v/jekyll-theme-chirpy)](https://rubygems.org/gems/jekyll-theme-chirpy) [![GitHub license](https://img.shields.io/github/license/cotes2020/chirpy-starter.svg?color=blue)][mit]

When installing the [**Chirpy**][chirpy] theme through [RubyGems.org][gem], Jekyll can only read files in the folders `/_data`, `/_layouts`, `/_includes`, `/_sass` and `/assets`, as well as a small part of options of the `/_config.yml` file from the theme's gem. If you have ever installed this theme gem, you can use the command `bundle info --path jekyll-theme-chirpy` to locate these files.

The Jekyll team claims that this is to leave the ball in the user’s court, but this also results in users not being able to enjoy the out-of-the-box experience when using feature-rich themes.

To fully use all the features of **Chirpy**, you need to copy the other critical files from the theme's gem to your Jekyll site. The following is a list of targets:

```shell
.
├── _config.yml
├── _plugins
├── _tabs
└── index.html
```

To save you time, and also in case you lose some files while copying, we extract those files/configurations of the latest version of the **Chirpy** theme and the [CD][CD] workflow to here, so that you can start writing in minutes.

## Prerequisites

Follow the instructions in the [Jekyll Docs](https://jekyllrb.com/docs/installation/) to complete the installation of the basic environment. [Git](https://git-scm.com/) also needs to be installed.

## Installation

Sign in to GitHub and [**use this template**][use-template] to generate a brand new repository and name it `USERNAME.github.io`, where `USERNAME` represents your GitHub username.

Then clone it to your local machine and run:

```
$ bundle
```

## Usage

Please see the [theme's docs](https://github.com/cotes2020/jekyll-theme-chirpy#documentation).

## Languages (English / Portuguese)

The site is bilingual via [jekyll-polyglot](https://github.com/untra/polyglot). English is the
default language and lives at the site root; Portuguese is served under `/pt-BR/`. Posts without
a Portuguese version automatically fall back to the English content (with a Portuguese UI).

To translate a post, copy it to `_posts/pt-BR/` **keeping the exact same file name** (that is what
links the two versions and keeps the URL stable), then translate the content and front matter
`title`/`description`:

```
_posts/2023-02-09-rtg-2-titanic.md        # English
_posts/pt-BR/2023-02-09-rtg-2-titanic.md  # Portuguese
```

The same applies to tabs, e.g. `_tabs/pt-BR/about.md` translates the About page.

The pieces that make this work (they shadow the theme gem's files, so revisit them when
upgrading the theme):

- `_config.yml` — `languages`, `default_lang`, and other polyglot settings
- `_includes/lang-selector.html` — the EN · PT switcher
- `_includes/sidebar.html` — includes the switcher in the sidebar bottom
- `_includes/lang.html` — makes UI strings follow the selected language
- `_includes/metadata-hook.html` — SEO `hreflang` alternate links
- `_layouts/default.html` — sets the `<html lang>` attribute per language
- `assets/css/jekyll-theme-chirpy.scss` — switcher styling

## License

This work is published under [MIT][mit] License.

[gem]: https://rubygems.org/gems/jekyll-theme-chirpy
[chirpy]: https://github.com/cotes2020/jekyll-theme-chirpy/
[use-template]: https://github.com/cotes2020/chirpy-starter/generate
[CD]: https://en.wikipedia.org/wiki/Continuous_deployment
[mit]: https://github.com/cotes2020/chirpy-starter/blob/master/LICENSE
