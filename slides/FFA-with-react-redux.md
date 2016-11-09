
# Introduction to React and Redux

- React: A JavaScript library for building UI
- Redux: A predictable state container for JavaScript apps.

They fit into the 2 building blocks of FFA

---

# Building View function with React

```js
  const View = (data) => (
    <div>
      hello ${data.name}
    </div>
  )
```
---

# Building composable View function with React

```js

  const Comment = (data) => (<li>{data.name}: {data.text}</li>)

  const CommentList = (data) => (
    <ul>
    {data.comments.map(commentData => <Comment {...commentData} />)}
    </ul>
  );

  const Post = (data) => (
    <div>
      <section>
        {data.postContent}
      </section>
      <section>
        <CommentList {...data.comments} />
      </section>
    </div>
  )
```

---

# Introduction to redux

Recall the formula we've got:

```
  currentState = actions.reduce(R, initialState)
```

The missing piece: updating the "array of actions".

---

# Dispatching actions in redux

In redux, `dispatch` is a special function that take an action, and returns nothing!

`dispatch` is:
- the only impure function in redux.
- the only way to update your view.

Each time dispatch is called with an action `A`, the `R` function is invoked with 2 arguments, the previous state and the action `A`. It returns and keeps a new state (and forget the previous state).

---

# Redux store

To make sure `dispatch` sends the actions to the right `R` function.
Redux has a concept of `store`.

A store is an object. It acts as a container of the current state.
Each Store has its own `R` function, that is called a `reducer`.
And a store also has a `dispatch` function that bound to that specific store.

```js
  const store = createStore(reducer, initialState);

  // calling the next line will send `action` to the above reducer function
  store.dispatch(action)
```

---

# Connecting react and redux.

We have 2 things now, the view function and the state. They live in their own places, in deep isolation.

To glue them together, we need:

1. a way to bind the special `dispatch` function to view.
1. a way to bind data to view

And the `react-redux` package has everything you need.

---

# Binding dispatch to view

Actions can come from many forms, a user mouse click, or server data arrival, or a timer tick. But in general, they are events.

You need to bind events to

---

# Pull data for view

Instead of keeping track of which part of the view needs to update when something in state changes, in FFA, views subscribe to state. In another word, whenever a piece in state changes, the whole app will re-render.

It sounds insane! Re-render the whole app for every changes (a keystroke in a text input produces a change.).

However, this is possible and instead very performant because we gave up power for principle. And principle like referential transparency (that we get when we stop mutating objects) provide a constant-time equality checking and optimizations like memoization.

---

# View subscribes state


# References
https://facebook.github.io/react/docs/thinking-in-react.html
https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367#.d0fwc83ec
