+++
title = "Cheatsheet for my macbook pro machine"
date = 2024-08-09
+++

This post will be updated regularly as i find something to say when i on my
macbook machine.

Some regular commands which are executed routinely to make this machine saner.

```shell
# update clojure
brew upgrade clojure

# update jenv versions (e.g) after brew upgrade
jenv add /usr/local/Cellar/openjdk/22.0.2/

# update java version to be used globally
jenv global 22.0.2

# gh cli
brew ugrade gh
gh extension install yusukebe/gh-markdown-preview
gh extension upgrade markdown-preview
```

---

How to print Github README

```bash
cd ... #// go to repo dir
gh markdown-preview
```

It will open browser tab to http://localhost:3333. We can then print this page
with `Command P` as usual, using scale of 75% seems okay to save same papers.
