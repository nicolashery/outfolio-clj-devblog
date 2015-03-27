# Day 1 (2015-03-26)

I needed to start somewhere, so I started with the backend REST API.

[Compojure](https://github.com/weavejester/compojure) seems to be the popular choice for routing on the server. It's built on top of [Ring](https://github.com/ring-clojure/ring), and described as the equivalent of Python's WSGI or Ruby's Rack (and maybe Node's Connect?).

I used mostly the following tutorials to get started, as well as the documentation of the libraries I'm using:

- http://adambard.com/blog/sinatra-docs-in-clojure/
- http://zaiste.net/2014/02/web_applications_in_clojure_all_the_way_with_compojure_and_om/
- https://devcenter.heroku.com/articles/clojure-web-application

[Leiningen](http://leiningen.org/) is the popular tools to manage dependencies, build and run the app. I created the project with the default template (`lein new outfolio-clj`) to stay minimal, but I guess I could've also used a Compojure or a Clojure+ClojureScript template.

After putting the basics of a Compojure app in `core.clj` and trying `lein run`, I'm greeted with one of the infamously usefull stack traces of Clojure ;)

```
Exception in thread "main" java.lang.IllegalArgumentException: No value supplied for key: true, compiling:(outfolio/core.clj:1:1)
[...]
```

After scratching my head for a while, turns out I was missing a closing bracket in one of the `:require` items on top of the file. This prompted me to look for a linting tool (like ESLint or JSHint for JavaScript), so I can easily catch these silly mistakes before running the app. Googling, I found the article [Clojure Code Quality Tools](http://blog.mattgauger.com/blog/2014/09/15/clojure-code-quality-tools/) which pointed to [Eastwood](https://github.com/jonase/eastwood). The output of Eastwood is more useful:

```
== Linting outfolio.core ==
Exception thrown during phase :eval of linting namespace outfolio.core
...
The following form was being processed during the exception:
(clojure.core/with-loading-context
 (clojure.core/refer 'clojure.core)
 (clojure.core/require
  '[clojure.tools.logging :refer [info]]
  '[compojure.core :refer :all]
  '[compojure.route :as route]
  '[org.httpkit.server
    :refer
    [run-server]
    [ring.middleware.defaults :refer :all]]))
...
```

I could easily see where that missing bracket was. Eastwood actually *loads* the code in the JVM, so you can get compile errors (with some additional useful information, as shown above). Another tool that looks nice to help you write more idiomatic Clojure is [Kibit](https://github.com/jonase/kibit). Anyway, back to work :)

This being an all-Clojure stack, I could've used [EDN](https://github.com/edn-format/edn) or even [Transit](https://github.com/cognitect/transit-format) as a data transport format, but I decided to stick with good old JSON to keep things simple and stay true to the original JavaScript version of Outfolio.

The rest of the day was quite slow, as I'm getting used to writing Clojure, and learning the basics of Compojure, Ring, handlers, request and response maps, etc. I got a simple JSON API working, with just a couple endpoints, and backed by an in-memory "database" (just an atom).

Sublime Text is my primary editor, but I'm using LightTable as well, I find it really useful as a "scratch pad" and to explore, evaluating things directly in source files with `Cmd+Enter` (`Cmd+Shift+Enter` to evaluate the entire file). I'll need to polish my workflow a bit, lots of `Ctrl+C` followed by `lein run` to see the results. I think it's possible to do some "live development" (see simple changes without restarting the app), but I'll leave that for another time.

Commit at end of day: [310cfe1](https://github.com/nicolashery/outfolio-clj/commit/310cfe144ebddf4e7cee77459b350edeb8f6f04f)
