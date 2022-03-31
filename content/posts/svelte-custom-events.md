+++
title = "Svelte: Custom Events"
date = "2022-03-30T16:24:11-07:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["svelte", "events", "typescript"]
keywords = ["svelte", "custom events"]
description = "A quick guide to setting up custom event listeners in svelte."
showFullContent = false
readingTime = false
hideComments = false
+++

Using `createEventDispatcher`, you can create a custom event dispatcher to dispatch _component events_.
The event dispatcher takes two arguments `name` (the event name) and `detail` which is the data you are
sending back.

```typescript
import { createEventDispatcher } from 'svelte';

// declare a event dispatcher
const dispatch = createEventDispatcher();

// dispatch an event
dispatch('event-name', eventData);
```

{{<code language="svelte" title="Counter.svete">}}
<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();
  export let finalCount: number = 10;
  export let count: number = 0

  const increment = () => {
    count += 1;

    if( count === finalCount ) {
      dispatch('message', {
        text: `Reached final count: ${finalCount}`
      });
    }
  }
</script>

<button on:click={increment}>Increment</button>
{{</code>}}

{{<code language="svelte" title="App.svelte">}}
<script lang="ts">
  import Counter from './Counter.svelte'

  let count = 0;
  function handleMessage(e: CustomEvent<{text: string}>) {
      alert(e.detail.text);
  }
</script>

<main>
  <p>Current counter: {count}</p>
  <Counter
    bind:count
    on:message={handleMessage}
    finalCount={15}/>
</main>
{{</code>}}
