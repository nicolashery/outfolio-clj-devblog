# Day 6 (2015-04-26)

Worked on adding functionality to the demo version of the frontend: creating new cards, editing and deleting existing cards.

Right now everything is synchronous (since we are not talking to the backend, it's all in-memory). At some point will have to make it asynchronous, and "fake" round trips to the server, adding things like a loading indicator. Will probably use `core.async` (vs. plain callbacks), but will see when I get there.

Still enjoy using Figwheel for the live-reload experience. I'm on OS X, and I wasn't using `rlwrap` (as suggested in the Figwheel README). I strongly recommend it since it allows you to use the keyboard arrows for corrections or going back in history. You just have to remember to use it:

```bash
$ brew install rlwrap
$ rlwrap lein figwheel
```

Also learned a useful trick for the REPL. You can easily jump from one namespace to the another to test out your functions by using:

```clojure
(in-ns 'outfolio.core)
```

When working on adding new cards, I realized that I want new cards to appear on top. However, I'm using a vector (`vec`), which supports easy addition to the end (with `conj`). A list (`list`) on the other hand, supports easy addition to the beginning (still with `conj`). This is a  bit confusing at first to beginners, but when I asked the question on Twitter I was told "in apps, just use vectors and conj". Simpler to have less choice :) However, this meant I should probably reverse my vector when displaying it, since I want most recent on top. It shouldn't be a performance hit since reverse is constant time.

```clojure
; pseudo-code
(def cards [{:_id "1" :name "foo"}])
; add a card (to the end of the vector)
(conj cards {:_id "2" :name "bar"})
; display cards most recent first
(reverse cards)
```

I'm not sure about using Om cursors to manage my application state. They feel like they have a bit too much "magic", but maybe that's because I'm not used to them. I think it's at least worth giving them a try, so that's what I'm doing. I'd probably feel more comfortable using a concept of stores ([Flux](http://facebook.github.io/flux/)), projections, in-memory database ([Re-frame](https://github.com/Day8/re-frame), [DataScript](https://github.com/tonsky/datascript), etc.). I'll have time to try that in the future. I'll probably have to re-think my app state a bit too. For instance, in the original Outfolio JS version, I have both a `cards` list and `card` object in-memory. In this Clojure version, if I edit the `card` object, I need to update it as well in the `cards` list (in the JS project, Backbone was handling that for me). Using an in-memory database might help with that.

When I move to async for data fetching and modification, I'll have to decide how I want to do it. I've seen (in the Figwheel example), that you can listen to changes in the app state atom by using `:tx-listen` when mounting the root Om component, and do your remote syncing there. But I might want finer control, to show a loading spinner etc., so maybe something ala CircleCI ([Brandon Bloom - Building CircleCI's Front end With Om](https://www.youtube.com/watch?v=LNtQPSUi1iQ)) which feels a bit like Flux.

Commit at end of day: [9813039](https://github.com/nicolashery/outfolio-clj/commit/9813039e8ca395e3e5b78e9b47aa832e4f9af6a7)
