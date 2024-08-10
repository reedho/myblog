+++
title = "Clojure Pills"
date = 2024-08-10
+++

## Practicalli's Clojure CLI Config

_Saturday, 10 Aug 2024_

Simplify our precious live with Practicalli's
[Practicalli Clojure CLI Config](https://github.com/practicalli/clojure-cli-config)

```bash
cd ~
mv .clojure .clojure__backup
git clone https://github.com/practicalli/clojure-cli-config .clojure
cd .clojure

# interesing stuff from the Makefile
make #// show available command from the makefile
make repl #// start repl
```

Golden outline for us who still could not memorize the clojure deps cli
configuration is available from the top of its `deps.edn` file. Here is the
repro.

Top-level keys for Clojure CLI deps.edn

- Practicalli Clojure CLI Config only defines aliases

  - `:aliases` - tools to enhance a development workflow

- Other top-level keys that could be used in a user or project `deps.edn`
  configuration:

  - `:mvn/repos` - Maven & Clojars repositories, examples of other repositories

  - `:mvn/local-repo` - set local repository path - defaults to
    `$HOME/.m2/repository`

  - `:paths` - directory paths to add to the class path

  - `:deps` - library dependencies to add to the class path

Keys used within `:aliases`

- resolve-deps aliases affect dependency resolution:

  - `:extra-deps` - libraries merged into the top-level `:deps` dependencies

  - `:replace-deps` - libraries replacing all those in the top-level `:deps`
    dependencies

  - `:override-deps` - library version overriding a matching library in the
    top-level `:deps`

  - `:default-deps` - library version if no version is otherwide defined, used
    across a set of shared projects acting as a dependency management system

- make-classpath aliases affect the classpath generation:

  - `:extra-paths` - additional paths to those defined in the top-level `:path`
    key

  - `:replace-paths` - paths replacing all those defined in the top-level
    `:path` key

  - `:override-paths` - alternate location to resolve a dependancy (overrides
    resolve-deps location)

- Configuration to run Clojure

  - `:main-opts` - clojure.main configuration options (vector of strings), e.g.
    `["--main" "fully.qualified.namespace"]` to set main namespace

  - `:exec-fn` - clojure.exec fully qualified function name to call (symbol)

  - `:exec-args` - clojure.exec arguments (hash-map) passed to `:exec-fn`
