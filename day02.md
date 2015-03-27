# Day 2 (2015-03-27)

Added a `POST` endpoint to the API, but needed to disable the `:anti-forgery` option in [ring-defaults](https://github.com/ring-clojure/ring-defaults). I use [Postman](https://chrome.google.com/webstore/detail/postman-rest-client-packa/fhbjgbiflinjbdggehcddcbncdddomop?hl=en) to try my API and was getting an "Invalid anti-forgery token" error back. I will add security later, by checking for an auth token on the request object.

```clojure
(def app-defaults
  ;; Allow CORS for POST requests, security handled by auth token in request
  (assoc-in site-defaults [:security :anti-forgery] false))
```

Instead of continuing to build endpoints using my "in-memory database", I decided to start using MongoDB now for persistence (same database as the original JavaScript Outfolio). The memory implementation could be useful for testing etc., but I would need to maintain it in parallel to the MongoDB implementation.

It looks like [Monger](http://clojuremongodb.info/) is what Clojurians use to talk to MongoDB. Although at the time of writing MongoDB has hit 3.0, I stayed on 2.6 because I wasn't sure if the Java and Clojure libraries are updated yet.

I had used an ORM in the JavaScript version of Outfolio ([Mongoose](http://mongoosejs.com/)), but I'm not a fan or ORMs anymore, so Monger looks good for me on this Clojure version. I will probably need to add some validation later ([Schema](https://github.com/Prismatic/schema) by Prismatic looks like a good choice).

I wasn't sure how to structure the app to manage the database connection. My first thought was to initiate the database connection in the `core.clj` file, but that would mean passing it all the way down (maybe attaching it to the `request` map?) to my database functions. Then I found [this snippet](https://github.com/luminus-framework/luminus-template/blob/master/src/leiningen/new/luminus/db/src/mongodb.clj) from the [Luminus](http://www.luminusweb.net/) framework and it looks like they create the connection directly in the database namespace, so I'll start with that for simplicity.

It was a bit painful to have to deal with ObjectIds, but that's pretty specific to using MongoDB. I decided that the interface of the functions in `outfolio.db` will accept strings as Ids, and the conversion to and from ObjectId will be done internally. This means `outfolio.api` has no idea that we're using MongoDB, it just calls function like `(db/get-card "123")` with plain hash maps and strings.

I certainly am still bitten by wrong placement or extra parentheses/brackets. I guess tooling like [ParEdit](http://www.emacswiki.org/emacs/ParEdit) could help with that. What happens is I get this huge stack trace error, and I really have no idea where to start looking. Usually I'm able to narrow it down to a function, which is good I guess, but I end up taking each expression in the function and evaluating it by hand in my "scratchpad" to try and figure out which one is causing trouble. One more reason to follow the "keep your functions small" guideline ;)

I definitely use LightTable more than SublimeText now, most notably because the inline-evaluation helps exploring and debugging.

Since I don't know all the Clojure functions by heart, the [ClojureScript Cheatsheet](http://cljs.info/cheatsheet/) is really my friend. Search is very fast, the grouping is well done, and you immediately see the function documentation on hover.

In the end, I was able to completely replace my in-memory "database" with the MongoDB implementation. I'll now be able to add the rest of the endpoints.

Commit at end of day: [d52a313](https://github.com/nicolashery/outfolio-clj/commit/d52a313647594cb2c90d9a203fed60851b2e604f)
