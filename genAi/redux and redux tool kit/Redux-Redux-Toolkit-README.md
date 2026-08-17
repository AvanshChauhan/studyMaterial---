# Redux & Redux Toolkit

A practical beginner-friendly guide to understanding Redux and Redux
Toolkit with React.

---

## Table of Contents

- [Why Redux?](#why-redux)
- [Redux Architecture](#redux-architecture)
- [Store](#store)
- [State](#state)
- [Actions](#actions)
- [Payload](#payload)
- [Reducers](#reducers)
- [Dispatch](#dispatch)
- [`useSelector()`](#useselector)
- [`useDispatch()`](#usedispatch)
- [Provider](#provider)
- [Redux Data Flow](#redux-data-flow)
- [Redux vs Redux Toolkit](#redux-vs-redux-toolkit)
- [`configureStore()`](#configurestore)
- [`createSlice()`](#createslice)
- [Reducers Inside a Slice](#reducers-inside-a-slice)
- [Generated Actions](#generated-actions)
- [Payload with Redux Toolkit](#payload-with-redux-toolkit)
- [Updating Objects](#updating-objects)
- [Arrays and `push()`](#arrays-and-push)
- [Multiple Slices](#multiple-slices)
- [Understanding State Paths](#understanding-state-paths)
- [Complete Redux Setup](#complete-redux-setup)
- [Common Beginner Mistakes](#common-beginner-mistakes)
- [Quick Mental Model](#quick-mental-model)
- [What's Next](#whats-next)

---

## Why Redux?

React already provides `useState`, so why do we need Redux?

### `useState`

`useState` is perfect for state that belongs to a particular component.

```js
const [email, setEmail] = useState("");
```

If only a login form needs the email, Redux is unnecessary.

### The shared state problem

Suppose multiple components need the same user data:

```text
Navbar
Profile
Dashboard
```

Passing the same data through multiple components can lead to **prop
drilling**.

Redux provides a central place for shared application state:

```text
             Redux Store
           /      |      \
       Navbar  Profile  Dashboard
```

### Simple rule

- `useState` → local/component state
- Context → simple shared state
- Redux → complex/global shared state

> Redux is not required for every React application.

---

## Redux Architecture

The basic Redux flow is:

```text
Component
    ↓
dispatch(action)
    ↓
Action
    ↓
Reducer
    ↓
State Update
    ↓
Store
    ↓
useSelector()
    ↓
Component/UI updates
```

### Memory trick

**Dispatch → Action → Reducer → State → UI**

---

## Store

The **Store** is the central place where Redux state is kept.

Example:

```js
{
  user: {
    name: "Avansh"
  },

  cart: {
    items: []
  },

  theme: "dark"
}
```

### Store vs State

- **Store** → container holding the state
- **State** → actual application data

---

## State

**State = the current data/value of the application.**

Example:

```js
{
  count: 10,
  user: "Avansh",
  isLoggedIn: true
}
```

Here:

- `10` is state data
- `"Avansh"` is state data
- `true` is state data

---

## Actions

An **Action** is a message describing what happened or what should
happen.

Example:

```js
{
  type: "counter/increment";
}
```

Another example:

```js
dispatch({
  type: "cart/addItem",
});
```

An action does **not directly contain the state-changing logic**.

The reducer handles that logic.

---

## Payload

**Payload = extra data sent with an action.**

Example:

```js
{
  type: "counter/add",
  payload: 5
}
```

Here:

```js
action.payload === 5;
```

Real example:

```js
dispatch(
  addItem({
    id: 1,
    name: "Shoes",
  }),
);
```

Then:

```js
action.payload;
```

is:

```js
{
  id: 1,
  name: "Shoes"
}
```

---

## Reducers

A **reducer** is the logic that decides how the state should change when
an action arrives.

Example:

```js
const reducer = (state, action) => {
  if (action.type === "counter/increment") {
    return {
      count: state.count + 1,
    };
  }

  return state;
};
```

### Remember

- `dispatch()` → sends the action
- **Reducer** → decides how state changes
- Store → holds the current state

---

## Dispatch

`dispatch()` sends an action to Redux.

```js
const dispatch = useDispatch();

dispatch(increment());
```

Think of it as:

> **dispatch = instruction bhejna**

It does not directly contain the state-changing logic.

---

## `useSelector()`

`useSelector()` is used to **read data from the Redux Store**.

```js
const count = useSelector((state) => state.counter.value);
```

If the store contains:

```js
counter: {
  value: 10;
}
```

Then:

```js
count === 10;
```

### Memory trick

> `useSelector()` = **data read karna**

---

## `useDispatch()`

`useDispatch()` gives us the `dispatch` function so we can send actions.

```js
const dispatch = useDispatch();

dispatch(increment());
```

### Memory trick

> `useDispatch()` = **action bhejna**

---

## `useSelector()` vs `useDispatch()`

Tool Purpose

---

`useSelector()` Read state
`useDispatch()` Dispatch actions

Example:

```js
const count = useSelector((state) => state.counter.value);

const dispatch = useDispatch();

dispatch(increment());
```

---

## Provider

React components need access to the Redux Store.

Use `Provider`:

```jsx
import { Provider } from "react-redux";
import { store } from "./store";

<Provider store={store}>
  <App />
</Provider>;
```

### What does Provider do?

It makes the Redux Store available to the React component tree.

Without the correct Provider setup, Redux hooks such as `useSelector()`
and `useDispatch()` cannot access the store.

---

## Redux Data Flow

When a user clicks a button:

```text
Button click
     ↓
dispatch(increment())
     ↓
Action
     ↓
Reducer
     ↓
State changes
     ↓
Redux Store updates
     ↓
useSelector() gets new state
     ↓
Component re-renders
     ↓
UI updates
```

### Example

```js
dispatch(increment());
```

Reducer:

```js
increment: (state) => {
  state.value += 1;
};
```

The value increases by `1`.

---

## Redux vs Redux Toolkit

### Redux

Redux is the state-management library/concept.

Its core concepts include:

- Store
- State
- Actions
- Reducers
- Dispatch

### Redux Toolkit

**Redux Toolkit (RTK) is the modern recommended way to write Redux.**

It reduces boilerplate and provides useful utilities such as:

- `configureStore()`
- `createSlice()`
- `createAsyncThunk()`

---

## `configureStore()`

### What is it?

`configureStore()` creates and configures the Redux Store.

### Basic syntax

```js
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: {
    // reducers go here
  },
});
```

With a slice:

```js
const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

This means the store has a `counter` section.

---

## `createSlice()`

### What is it?

`createSlice()` lets us define:

- slice name
- initial state
- reducers

It also automatically generates action creators.

### Syntax

```js
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",

  initialState: {
    value: 0,
  },

  reducers: {
    increment: (state) => {
      state.value += 1;
    },

    decrement: (state) => {
      state.value -= 1;
    },
  },
});
```

### Simple meaning

> `createSlice()` = state + reducers + generated actions in one place.

---

## Reducers Inside a Slice

Example:

```js
reducers: {
  increment: (state) => {
    state.value += 1;
  },

  decrement: (state) => {
    state.value -= 1;
  }
}
```

When `increment` is dispatched:

```text
increment action
      ↓
increment reducer
      ↓
value += 1
```

---

## Why Can Redux Toolkit Mutate State Directly?

This is valid in Redux Toolkit:

```js
state.value += 1;
```

Redux Toolkit uses **Immer internally**.

Immer allows us to write code that looks like mutation while safely
producing the next immutable state.

> This does not mean normal Redux code can freely mutate state.

---

## Generated Actions

Given:

```js
const counterSlice = createSlice({
  name: "counter",

  initialState: {
    value: 0,
  },

  reducers: {
    increment: (state) => {
      state.value += 1;
    },
  },
});
```

Export the generated action:

```js
export const { increment } = counterSlice.actions;
```

Then:

```js
dispatch(increment());
```

Redux Toolkit creates an action with a type similar to:

```js
{
  type: "counter/increment";
}
```

### Pattern

```text
slice name + reducer name
        ↓
"counter/increment"
```

---

## Payload with Redux Toolkit

Reducer:

```js
add: (state, action) => {
  state.value += action.payload;
};
```

Component:

```js
dispatch(add(20));
```

The resulting action is roughly:

```js
{
  type: "counter/add",
  payload: 20
}
```

Therefore:

```js
action.payload === 20;
```

---

## Updating Objects

Suppose:

```js
initialState: {
  user: {
    name: "Avansh",
    role: "admin"
  }
}
```

Reducer:

```js
changeRole: (state, action) => {
  state.user.role = action.payload;
};
```

Then:

```js
dispatch(changeRole("member"));
```

Result:

```js
{
  user: {
    name: "Avansh",
    role: "member"
  }
}
```

Only `role` changes.

### Important distinction

```js
state.user.role = action.payload;
```

→ changes only `role`.

```js
state.user = action.payload;
```

→ replaces the entire `user` object.

---

## Replacing an Entire Object

Reducer:

```js
updateUser: (state, action) => {
  state.user = action.payload;
};
```

Then:

```js
dispatch(
  updateUser({
    name: "Rahul",
  }),
);
```

Result:

```js
{
  user: {
    name: "Rahul";
  }
}
```

The previous `role` is gone because the entire `user` object was
replaced.

---

## Arrays and `push()`

Example:

```js
initialState: {
  items: [];
}
```

Reducer:

```js
addItem: (state, action) => {
  state.items.push(action.payload);
};
```

Then:

```js
dispatch(
  addItem({
    id: 1,
    name: "Shoes",
  }),
);
```

Result:

```js
items: [
  {
    id: 1,
    name: "Shoes",
  },
];
```

Another dispatch adds another item:

```text
[]
 ↓
[Shoes]
 ↓
[Shoes, T-Shirt]
```

`push()` adds to the existing array.

---

## Multiple Slices

Real applications usually have multiple slices.

```text
Redux Store
├── user
├── cart
├── products
└── auth
```

Configure them:

```js
const store = configureStore({
  reducer: {
    user: userReducer,
    cart: cartReducer,
    products: productsReducer,
  },
});
```

This keeps application state organized by feature.

---

## Understanding State Paths

If the store is:

```js
{
  counter: {
    value: 10
  },

  user: {
    name: "Avansh",
    role: "admin"
  },

  cart: {
    items: [],
    total: 500
  }
}
```

Selectors:

```js
state.counter.value;
```

→ `10`

```js
state.user.name;
```

→ `"Avansh"`

```js
state.user.role;
```

→ `"admin"`

```js
state.cart.total;
```

→ `500`

### Pattern

```text
state
  ↓
slice name
  ↓
specific property
```

---

# Complete Redux Setup

## `counterSlice.js`

```js
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",

  initialState: {
    value: 0,
  },

  reducers: {
    increment: (state) => {
      state.value += 1;
    },

    decrement: (state) => {
      state.value -= 1;
    },

    add: (state, action) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, add } = counterSlice.actions;

export default counterSlice.reducer;
```

---

## `store.js`

```js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

The resulting state structure is:

```js
{
  counter: {
    value: 0;
  }
}
```

---

## `main.jsx`

```jsx
import { Provider } from "react-redux";
import { store } from "./store";

<Provider store={store}>
  <App />
</Provider>;
```

Now the React component tree can access the Redux Store.

---

## Component

```jsx
import { useSelector, useDispatch } from "react-redux";

import { increment, decrement, add } from "./counterSlice";

function Counter() {
  const count = useSelector((state) => state.counter.value);

  const dispatch = useDispatch();

  return (
    <>
      <h1>{count}</h1>

      <button onClick={() => dispatch(increment())}>+</button>

      <button onClick={() => dispatch(decrement())}>-</button>

      <button onClick={() => dispatch(add(5))}>Add 5</button>
    </>
  );
}
```

---

# Common Beginner Mistakes

## 1. Thinking `dispatch()` directly changes state

Wrong mental model:

```text
dispatch → directly changes state
```

Correct:

```text
dispatch
  ↓
action
  ↓
reducer
  ↓
state update
```

---

## 2. Confusing Store and State

- Store = container
- State = data inside the container

---

## 3. Using the Wrong Selector Path

If the store is:

```js
{
  user: {
    name: "Avansh";
  }
}
```

Correct:

```js
state.user.name;
```

Incorrect:

```js
state.name;
```

---

## 4. Thinking Every React State Needs Redux

It doesn't.

Use local state when the data is only needed by one component.

```js
const [email, setEmail] = useState("");
```

There is no reason to put a simple form input into Redux just because
Redux is available.

---

## 5. Confusing Object Update and Object Replacement

```js
state.user.role = "member";
```

Changes one property.

```js
state.user = {
  name: "Rahul",
};
```

Replaces the entire object.

---

# Quick Mental Model

Think of Redux like an office:

```text
Component
   ↓
Dispatch = sends a request
   ↓
Action = describes the request
   ↓
Reducer = decides what should happen
   ↓
Store = keeps current data
   ↓
Selector = reads the data
   ↓
Component = shows updated UI
```

---

# Most Important Redux Concepts

Concept Main Job

---

Store Holds application state
State Actual application data
Action Describes what happened
Payload Carries extra data
Reducer Decides how state changes
`dispatch()` Sends an action
`useSelector()` Reads state
`useDispatch()` Gives access to dispatch
`Provider` Makes Store available to React
`configureStore()` Creates/configures Store
`createSlice()` Creates state + reducers + actions

---

# What to Learn Next

The next stage of Redux is handling **asynchronous operations**.

Recommended order:

1.  `createAsyncThunk`
2.  API calls with Redux
3.  Loading states
4.  Success states
5.  Error states
6.  Redux DevTools
7.  Common Redux mistakes
8.  Redux best practices
9.  Real-world Redux project

---

## Core Formula

```text
READ STATE
    ↓
useSelector()

CHANGE STATE
    ↓
dispatch()

STATE-CHANGE LOGIC
    ↓
reducer

CENTRAL DATA
    ↓
Store

EXTRA ACTION DATA
    ↓
payload
```

> **Redux Toolkit makes Redux easier to write, but understanding the
> underlying flow is more important than memorizing the syntax.**
