+++
title = "Setting Up Persistent Authentication With Firebase 9, React and Redux"
date = "2022-12-19T20:46:55-08:00"
author = "Amy Dang"
authorTwitter = "rxkironto" #do not include @
cover = "img/firebase-react-redux-typescript.jpg"
tags = ["redux", "webdev", "react", "firebase", "typescript"]
keywords = ["redux", "webdev", "react", "firebase", "typescript", "persistent", "authentication", "web development", "react-redux"]
description = "Learn how to set up persistent authentication in React using Firebase and Redux"
showFullContent = false
readingTime = true
hideComments = false
draft = false
+++

While working on one of my projects, I had a bit of trouble trying to add persistent
authentication with Firebase and so this is my solution. I don't want this guide
to be too long, so I will not be covering how to implement Firebase sign-in and
sign-out.

Hope it helps someone! :)

## Dependencies

Make sure to already have a React project and a Firebase project created. As
well as to install npm packages for both `firebase` and `react-redux`.

You can install them with:

```bash
npm install @reduxjs/toolkit react-redux firebase
```

## Setting up Firebase

Before everything, make sure to set up your firebase config. I am using typescript,
so this file will be called `firebase.ts`. In my project, I will be placing this
file in a `utils` folder.

```typescript
// file: utils/firebase.ts
const firebaseConfig = {
  /*
     COPY PASTE YOUR CONFIG HERE
  */
  apiKey: "xxxxxxxxxxxxxxxxxxxx",
  authDomain: "xxxxxxxxxxxxxxxxxxxx",
  projectId: "xxxxxxxxxxxxxxxxxxxx",
  storageBucket: "xxxxxxxxxxxxxxxxxxxx",
  messagingSenderId: "xxxxxxxxxxxxxxxxxxxx",
  appId: "xxxxxxxxxxxxxxxxxxxx",
};

const firebaseApp = initializeApp(firebaseConfig);
const auth = getAuth(firebaseApp);

export { auth, firebaseApp };
```

### Add a provider in firebase

If you haven't already, we will need to add a provider in Firebase in order
to enable sign-in using email and password.

In the Firebase console, go to your
`Authentication` dashboard and then click on the `Sign-in method` tab. From here,
we should be able to see a `Add new provider` button.
Click on this button and then under `Native Providers`, enable `email/password`.

## Setting up Redux

If you have already set up Redux in your project, feel free to skip this section!
According to the official Redux docs, we should create slices within a folder
called `features`. I prefer to have all my Redux related files organized within
a single a folder. So we'll be adding all of our Redux related files within a
folder called `store`.

### What our folder structure will look like

This will be how our Redux folder structure will look like.

```text
   src/store
   ├── features
   │ └── user
   │  └── userSlice.ts
   ├── index.ts
   └── reducers.ts
```

### Create our store

In our `store` folder create a file called `index.ts`. This is where we will
be setting up our Redux store. We name the file `index.ts` so that when we export
our Redux store, we can easily import it to our app with
`import store from './store';`

Place this code in `store/index.ts`:

```typescript
// file: src/store/index.ts
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: () => ({}),
});

export default store;
```

Then we will need to make sure we provide the store to our entire app.
Within our React project and in the file where you mount your root app
(this should be in `src/index.ts`), import `Provider` from `react-redux` and
our Redux store. Then wrap your App component with the component `<Provider>`.

```typescript
// file: src/index.ts
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";

import App from "./App";
import store from "./store";

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

If you need more information on how all of this code works, you can check out
Redux's official docs on [getting started with Redux](https://redux.js.org/introduction/getting-started).

### Creating our user slice

The user slice will be responsible for all of our state logic and actions for
users. You can find more information on slices in the
[official Redux docs](https://redux.js.org/tutorials/essentials/part-2-app-structure#redux-slices).
In our user slice, a user is logged out when the state is null and logged in
when there is User data.

```typescript
// file: ./store/features/user/userSlice

import { createSlice, PayloadAction } from "@reduxjs/toolkit";
import { User } from "firebase/auth";

interface UserState {
  user: User | null;
}

const initialState: UserState = {
  user: null,
};

const userSlice = createSlice({
  name: "user",
  initialState: initialState,
  reducers: {
    logout() {
      return {
        ...initialState,
      };
    },

    login(state: UserState, action: PayloadAction<User>) {
      return {
        user: action.payload,
      };
    },
  },
});

// selectors
export const selectUser = (state: UserState) => state.user;

// actions
export const { startLoading, stopLoading, login, logout } = userSlice.actions;

export default userSlice.reducer;
```

With the reducers file in place, we can now add our user reducer to our list of
reducers.
I like to create a separate file for this so that the `store/index.ts` file is
a bit cleaner. So we will create a new file called `reducers.ts` in the `store`
folder to hold all of our reducers. And to combine our reducers, we will use a
built-in Redux method called `combineReducers`.

Here's what this code will look like:

```typescript
// file: ./store/reducers.ts

import { combineReducers } from "redux";

import userReducer from "./features/user/userSlice";

const rootReducer = combineReducers({
  user: userReducer,
  // OTHER REDUCERS HERE
});

export default rootReducer;
```

You also need to make sure the store knows about these
reducers. So we will also update `./store/index.ts`:

```typescript
// file: ./store/index.ts
import { configureStore } from "@reduxjs/toolkit";
import rootReducer from "./reducers";

const store = configureStore({
  // NEW CODE
  reducer: rootReducer,
  // NEW CODE
});

export default store;
```

## Loading persistent user data on mount

Ok! We've finally gotten redux and firebase up and running. Now to make user
login persistent.

In order to watch for changes in our Firebase authentication state, we will
need to use the method `onAuthStateChange`. It is a `observer` that watches for
changes in our Firebase authentication state. And since it's an observer, we
will also need to make sure we clean it up by unsubscribing to the observer
when the component unmounts.

```typescript
import { useEffect } from "react";
import { useDispatch } from "react-redux";
import { login, logout } from "./store/features/user/userSlice";

function App() {
  const dispatch = useDispatch();
  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (userAuth) => {
      if (userAuth) {
        dispatch(login(userAuth));
      } else {
        dispatch(logout());
      }
    });
    return () => {
      unsubscribe();
      dispatch(logout());
    };
  }, []);

   return (
     // ...
     // component logic here
     // ...
   );
}
```

### Accessing user data within a React component

In order to access our user's data and login status, we need to import
`selectUser` which we've already implemented in our `userSlice.ts` file.
Combined with `useSelector`, we will be able to grab the data from within our
Redux store.

```typescript
import { selectUser } from "./store/features/user/userSlice";
import { useSelector } from "react-redux";
import type { User } from "firebase/auth";

function myComponent() {
  const user: User | null = useSelector(selectUser);

  // ...
  // component logic here
  // ...
}
```

Annnnd we are done. Let me know if there are any mistakes :D
