+++
title = "Bismillah"
date = 2024-07-30
+++

Bismillahirrohmanirrohim.

<br />

This is the steps taken to launch this blog.

```bash
# install on my machine which is a fedora
sudo dnf copr enable fz0x1/zola
sudo dnf install zola

# init
cd ~
zola init myblog # answer the questions, i choose y for all the things
cd myblog
git init

# install a theme, i choose hook for now
git submodule add https://github.com/InputUsername/zola-hook.git themes/hook

# using `hook` theme, at least two files needs to be created
# we can for example copy from the example given by the template
mkdir -p content/blog
cp themes/hook/content/_index.md content/_index.md
cp themes/hook/content/bloc/_index.md content/blog/_index.md

# Set some values in config.toml, see below.

# its ready now!

# start dev live-reload server
zola serve # then open the dev server in http://localhost:1111

# build
zola build # this will build to public directory

git add .
git commit -m "Bismillah"

# setup deployment to cloudflare pages with wrangler
pnpm init
pnpm add --save-dev wrangler
nvim package.json # add deploy in script section, see below.

# deploy
pnpm run deploy
```

<br />

This is the content of `config.toml`:

```toml
# The URL the site will be built for
base_url = "https://datafy.id/"

# Whether to automatically compile all Sass files in the sass directory
compile_sass = true

# Whether to build a search index to be used later on by a JavaScript library
build_search_index = true

theme = "hook"

[markdown]
# Whether to do syntax highlighting
# Theme can be customised by setting the `highlight_theme` variable to a theme supported by Zola
highlight_code = true

[extra]
# Put all your custom variables here
links = [
    { title = "Datafy", href = "https://datafy.id" },
    # ...
]
```

<br />

`package.json` file:

```json
{
  "name": "myblog",
  "version": "1.0.0",
  "description": "",
  "scripts": {
    "build": "zola build",
    "publish": "wrangler pages deploy --project-name=myblog --branch=main --commit-dirty=true ./public",
    "deploy": "pnpm run build && pnpm run publish"
  },
  "devDependencies": {
    "wrangler": "^3.67.1"
  }
}
```

<br />

Thats it! See the source for your self [here](https://github.com/reedho/myblog).
