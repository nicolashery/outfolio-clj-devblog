# Day 3 (2015-03-31)

Writing more Clojure means getting used to its syntax and learning from my silly mistakes :) For example, if you want to "return" the `card` hash map from a function or `let` binding, you simple write `card` as the "body", not `(card)`. The latter form will try to use the hash map as a function (yes, hash maps can be functions, like `(card :_id)` will return the card's id), and throw a "wrong number of arguments" error.

```clojure
(let [card-owned-by-user? (= user-id (get-in card [:owner :_id]))
      ;; this will not work, it will try to "call" to boolean and hash map
      card (if (card-owned-by-user?) (card))
      ;; this will work
      card (if is-card-owned-by-user? card)]
```

I also learned that `defn-` and `def-` can be used to define functions and vars that are private to the namespace.

I saw the `foo*` naming convention in a few places on GitHub, and it seems to be used to indicate an "intermediate thing" to get to `foo`, which is what you want to use. I decided I'll use it for my routes before wrapping them in middleware:

```clojure
(defroutes api-routes*
  (GET "/api/cards" [] get-cards)
  (GET "/api/cards/:card-id" [] get-card))

(def api-routes
  (-> api-routes*
      (wrap-json-body)
      (wrap-json-response)))
```

I'm trying to use Paredit a bit more too, starting with "slurp" and "barf".

Most of my time today was spent trying to do a bit of "refactoring" in `outfolio.api`. Indeed, I had common functionality in the handlers of these routes:

- `GET /api/cards/:card-id`
- `PUT /api/cards/:card-id`
- `DELETE /api/cards/:card-id`

All of these handlers were fetching the card from the database for the corresponding `:card-id`, and if it didn't exist or the user wasn't the owner of the card, they would all send the same 404 "card not found" response. I thought this was a good use case for a Ring [Middleware](https://github.com/ring-clojure/ring/wiki/Middleware-Patterns) that would add a `:card` key to the `request` map, or return a 404 response otherwise ([Middleware Patterns](https://github.com/ring-clojure/ring/wiki/Middleware-Patterns)).

This allowed me to learn more about middlewares, and the things they can do (similar to middlewares in Express for Node.js):

```clojure
(defn wrap-something [handler]
  ;; a middleware returns a new handler
  (fn [request]
    ;; you can add something to the request...
    (handler (assoc request :card card))
    ;; ...or return a response (which will stop the calling of handlers)
    (status (response {:error "Card not found"}) 404)
    ;; ...or add something to the response
    (assoc-in (handler request) [:headers "Content-Type"] "text/plain")
    ))
```

I struggled a little bit to figure out how I could add my `wrap-card` middleware to only the 3 routes mentioned above, and if I should be careful with the ordering so it plays nice with the other middlewares like `wrap-json-body` and `wrap-json-response`.

I ended up wrapping each route individually (saw it done in the [Friend](https://github.com/cemerick/friend) library), and it seemed to work fine:

```clojure
(defroutes api-routes*
  (GET "/api/cards" [] get-cards)
  (POST "/api/cards" [] post-card)
  (GET "/api/cards/:card-id" [] (wrap-card get-card))
  (PUT "/api/cards/:card-id" [] (wrap-card put-card))
  (DELETE "/api/cards/:card-id" [] (wrap-card delete-card)))
```

It made sense to use the `context` Compojure macro ([Nesting routes](https://github.com/weavejester/compojure/wiki/Nesting-routes)) to get rid of the repetitive `"/api/"` prefix in the routes definition (also useful if we want to do API versioning). I also added a proper "not found" JSON response to the API routes.

```clojure
;; outfolio.api
(defroutes api-routes*
  (GET "/cards" [] get-cards)
  (ANY "*" [] not-found))

;; outfolio.core
(defroutes app-routes
  (context "/api" [] api/api-routes)
  (GET "/" [] "Hello World!")
  (route/resources "/")
  (route/not-found "Not Found"))
```

Commit at end of day: [da7b876](https://github.com/nicolashery/outfolio-clj/commit/da7b8762e4844c2ea24459f2aebb7b99c567813b)
