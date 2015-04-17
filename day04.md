# Day 4 (2015-04-01)

I needed to start showing some HTML. Before diving into the ClojureScript "single-page app", I wanted to set up some basic server-side HTML views. I'll need one for the home page, for the "app" page, and also for the authentication flow which is done on the server (via Google OAuth2).

Popular choices seem to be:

- [Hiccup](https://github.com/weavejester/hiccup): HTML as Clojure data structures
- [Selmer](https://github.com/yogthos/Selmer): like Django templates
- [Stencil](https://github.com/davidsantiago/stencil): Mustache templates
- [Enlive](https://github.com/cgrand/enlive): "selector-based" templating engine

I went with Hiccup because it looked simple and represented HTML as plain Clojure data structures (vectors and hash maps). It might be a little harder to read than HTML tags if templates get big, but for my use-case it seemed like a good place to start.

I was struggling a bit to understand in what order I had to put my Ring middlewares. This [StackOverflow answer](http://stackoverflow.com/questions/19455801/why-does-the-order-of-ring-middleware-need-to-be-reversed) helped understand why the order is "reversed" (i.e. why I had to put my `wrap-log-request` middleware first so it logs *after* all other middlewares have been applied).

The rest of the day was spent trying to understand more about sessions, middlewares, and thinking on how I will handle authentication.

Commit at end of day: [4f0dd17](https://github.com/nicolashery/outfolio-clj/commit/4f0dd173dc78b798450d95ff3457c72a983d1cdf) 
