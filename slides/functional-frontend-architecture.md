class: middle, center

# Functional Front-end Architecture

---

# Agenda

1. Introduction to Functional Front-end Architecture
1. When and When not to use it
1. View function
1. State function
1. Updating view
1. Reactivity

*note: this slide contains only pseudo code*

---

# About me

1. JavaScript specialist
1. playing with JavaScript in browser since 2005
1. working with JavaScript fullstack since 2012
1. FE: sammy, knockout, angular, react (thanks god! no backbone)
1. BE: express, meteor, redis, mongo, amqp
1. Love to build web-scale apps and services

@tung_js

---

# What is Functional Front-end architecture

A Front-end (more precisely, presentation layer) architecture that embraces pure function and unidirectional data flow.

*pure function* is a function that
- given a same set of params, always returns a same value
- has no side effect
- you **cannot mutate anything**

---

# Why Functional Front-end architecture

- **predictable**: given a state, you will always render a same view.
- **reproducible**: given an initial state and a list of user interactions, you can always reproduce the whole rendering history
- **modularity**: building a giant app from smaller single-purpose components
- **testability**: take testing to a whole new level, there is nothing easier than testing pure functions.



---

# Trade-offs

To enable Functional Front-end architecture. You must:

1. Describe every component in a declarative way
1. Describe application state as plain objects and arrays.
1. Describe changes in the system as plain objects.
1. Describe the logic for handling changes as pure functions.

---

# Trade-offs
These restrictions are not required to build conventional apps.
So mindset shifting required.

Applying those constraints will limit your power with your programming language.
The key is to find the balance between:

__*power* vs *principle*__

---

# Building blocks in FFA

1. a pure view function
2. a pure state function

---

# View function

In FFA, you render view with a pure function `V` as following:

```js
  view = V(state)
```

* bring up predictable

---

# State function

In FFA, you update states with a pure function `R` as following:

```js
  nextState = R(previousState, nextAction)
```

so we can retrieve the current state by apply `R` on an array of actions

```js
  currentState = actions.reduce(R, initialState)
```

* bring up reproducible

---

# Combine state and view

Now, let's combine `V` and `R`.

```js
  currentView = V(actions.reduce(R, initialState))
```

Given a same array of actions, you get the same view, every time, always!

---

# Combine state and view

In real-life apps, view needs data to be transformed before being consumed. We need another pure function `S` that:

```js
  consumableState = S(rawState)
```

and the formula becomes

```js
  currentView = V(S(actions.reduce(R, initialState)))
```

---

# How to change the view

In the formula we formed above:

- `V` is a pure function
- `S` is a pure function
- `R` is a pure function
- `initialState` is a constant object that never changes

The only way to update view is by changing the "array of `actions`".
We'll do it by "`push`ing" new actions to that "array" and rerun the formula.

---

# When to change the view

Changes arrive in many forms:

1. user interactions with the view (mouse clicks, keystrokes, voice command, etc)
2. data arrival from server (http responses, websocket messages, etc.)
3. timer ticks (think of `setTimeout`, `setInterval`, etc.)

But in general, let's call them events.

---

# Pushing event to "array of actions"

Whenever an event that we interested in happens. We "push" that event to the "array of actions" a s following:

```js
while (true) {
  event = waitForEvent();
  actions.push(event);
}
```

---

# Actions as plain objects

Unfortunately, `event`s are not always plain objects. We can only push plain objects to the "array of actions".
We need a pure function `A` which takes an `event` and returns a plain object representing an action.

```js
  action = A(event)
```

---

# Combine everything

```js
while (true) {
  event = waitForEvent();
  actions.push(A(event));
  currentView = V(S(actions.reduce(R, initialState)))
  setHTML(currentView)
}
```

This pretty much everything about FFA. Without reactive function programming methods.

---

# Reactivity

RFP is an extension of functional programming, solving the only problem of a pure FP paradigm, which is absolute immutability.
In the previous code, we mutated the `actions` array by using push. And RFP solves that particular problem elegantly:

```js
  actions$.scan(R).startWith(initialState).map(S).map(V).subscribe(setHTML);

  while (true) { actions$.next(A(waitForEvent())); }
```

---

# Renaming

Let give everything a better name:

```js
  actions$
    .scan(reducer)
    .startWith(initialState)
    .map(selector)
    .map(render)
    .subscribe(setHTML);

  while (true) {
    actions$.next(
      actionCreator(waitForEvent())
    );
  }
```

---

# Conclusions

FFA is not something I invented or even new.
It's also called the Elm architecture as Elm makes this popular
(Elm is actually designed with those concept at the first place)

If you are not comfortable with a whole new language, there are many solutions to apply FFA in JavaScript:

Declarative view libraries: React, Preact, snabbdom, etc.
Predictable state container lib: Redux, MobX, Cycle.js

Those libraries provide exactly what is described in this architecture.
Choose what fits and start building your application.
