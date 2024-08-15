+++
title = "My ShadowCljs Playground Setup"
date = 2024-08-09
+++

In this post we will document the setup for playing around with ShadowCljs on my
machine.

This post will be updated periodically as we progress in taming this
environment.

As of today (08-Aug-2024), The fast js runtime and toolkit
[`bun`](https://bun.sh) is used for js package manager and for whatever task it
could be used for.

## Setup

```bash
bun init
cd shadowcljs_playground
bun add --dev shadow-cljs

#// verify if shadow-cljs installed properly
bunx shadow-cljs help

bunx shadow-cljs init #// this will create `shadow-cljs.edn` file

#// this will download required clojure libs as necessary and show project details
bunx shadow-cljs info

#// this will start node js repl, type :cljs/quit at repl prompt to exit
bunx shadow-cljs node-repl

#// note that as of now running node-repl with completely bun is still not working.
bunx --bun shadow-cljs node-repl # NOT WORKING!

#// this will start browser repl in terminal, open browser tab to be used as the runtime.
bunx shadow-cljs browser-repl
```

At this point, we can use emacs and cider jack-in. Just visit `shadow-cljs.edn`
on the folder and answer the prompt accordingly.

```
M-x cider-jack-in-cljs
shadow RET
browser-repl RET
```

Now we configure a build:

```clojure
{
 ,,,
 :builds
 {:app {:target     :browser
        :output-dir "public/assets/app/js"
        :asset-path "/assets/app/js"
        :modules    {:main {:entries [my.app]}}}}
}
```

As a simple example, now we will add some npm package and create a demo source
file.

```bash
bun add uid
```

File `./src/main/my/app.cljs`:

```clojure
(ns my.app
  (:require ["uid" :as uid]))

(defn ^:export hello
  []
  (let [x (uid/uid)]
    (println x))
  )
```

With this build setting and the source file, now we can:

```bash
bunx shadow-cljs compile app # compile for development build
bunx shadow-cljs release app # compile for release build
```

Now we can verify the release build result with some thing like this.

```bash
pushd public/assets/app/js  #// go to output dir
bun --print 'require("./main.js").my.app.hello()' #// this will print random uid to stdout
popd
```

For development, add key value `[:dev-http {8000 "public"}]` to
`shadow-cljs.edn` and setup minimal `public/index.html` file.

```html
<html>
  <head>
    <script src="/assets/app/js/main.js"></script>
  </head>
  <body>
    <p>This is dummy development page for serving cljs repl</p>
  </body>
</html>
```

Then, from `emacs`, do `M-x cider-jack-in-cljs` `RET` `shadow` `RET` `:app`
`RET` `y`. This will open browser tab to dev-http server in
`http://localhost:8000`. This will also start shadowcljs dashborad in
`http://localhost:9630/dashboard`.

Verify if all things works as expected e.g. by typing below code in the repl.

```clojure
(-> (js/fetch "https://api.ipify.org")
    (.then #(.text %))
    (.then #(tap> %)))
```

## Cljs Libs

- https://github.com/leonoel/missionary - `[missionary/missionary "b.39"]`
- https://github.com/mfikes/cljs-bean - `[cljs-bean/cljs-bean "1.9.0"]`
- https://github.com/applied-science/js-interop - `[applied-science/js-interop "0.4.2"]`
- https://github.com/hyperfiddle/rcf - `[com.hyperfiddle/rcf "20220926-202227"]`

## Day to day tips

To inspect a npm package, use this.

```clojure
(require '["uid" :as uid])
uid ;; => #js {:uid #object [Function]}
(uid/uid) ;; => "7d60fc94426"
```

To add cljs dependencies, add the library vector to `shadow-cljs.edn` like this.

```clojure
{,,,

 :dependencies
 [[missionary/missionary "b.39"]]

 ,,,}
```

While we are already connect to cljs repl in emacs, we can connect to its
sibling clj repl.

```
M-x cider-connect-sibling-clj
```

To access ShadowCljs toolings from the clojure repl.

```clojure
;; while we are at shadow.user ns in repl,
;; there is ns alias `shadow` which point to the devtools api.
(require '[shadow.cljs.devtools.api :as shadow])

(shadow/help)
(shadow/active-builds) ;; show the active builds
(shadow/get-build-config :app) ;; show the build config of :app
(shadow/get-config) ;; show actual shadow-cljs build config
```
