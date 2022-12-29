+++
title = "React Lifecycle: useEffect Hook"
date = "2022-12-29T11:19:43-08:00"
author = "Amy Dang"
authorTwitter = "rxkironto" #do not include @
cover = ""
tags = ["react", "react-hooks", "webdev"]
keywords = ["react", "react hooks", "webdev", "reactjs", "js", "javscript", "notes"]
description = "Here are some notes I have on the useEffect hook that I tend to forget."
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
videos = []
+++

Allows us to think about when things should happen in relation to state changes.

```javascript
function MyComponent() {
  React.useEffect(() = >{
    // Runs whenever state changes

    return function cleanup() {
      // This will cleanup function run at the end of the life of the component
      // Mostly used to clean up things like timeout or subscriptions
    }
  }, [when, any, ofThese, variables, change]);

  React.useEffect(() = >{
    // Re-runs every single time the component is re-rendered
    // and runs at first render
  });

  React.useEffect(() = >{
    // runs only once after the component has mounted
  }, []);

  return ( <div>hello world</div> );
}
```

```
🔥 Tip:
You can use multiple effects to separate concerns.

```

## Gotchas

When you pass in a state into `useEffect` as a second argument, make sure that
you actually include all values from the component scope that change over time
and are used by the effect.

If you **DO NOT**, React could render `stale values` from previous renders.

```
React relies on the order in which Hooks are called. In other words, each React
Hook gets called based on the order that they are declared on every render.
```
