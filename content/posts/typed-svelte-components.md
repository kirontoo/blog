+++
title = "Svelte: Components with Typed Props"
date = "2022-03-30T15:47:03-07:00"
author = "Amy Dang"
authorTwitter = "" #do not include @
cover = ""
tags = ["svelte", "typescript", "webdev"]
keywords = ["svelte", "typescript", "components", "typed", "props"]
description = "How to set up typed props for svelte components with typescript."
showFullContent = false
readingTime = false
hideComments = false
draft = true
+++

How to set up typed props for svelte components.

Use `Button.svelte.d.ts`.

Given a Button svelte component.

**Example Button.svelte**:

{{< code language="tsx" title="Buttons.svelte" id="1" expand="Show" collapse="Hide" isCollapsed="false" >}}
<script lang="ts">
    export let disabled: boolean = false;
    export let kind: "primary"
        | "danger"
        | "secondary"
        | "ghost"
        | "outline"  = "primary";
    export let size: "sm" | "md" | "lg" = "md";
    export let type: string = "button";
</script>

<button
    class:btn={true}
    class:btn-disabled={disabled}
    class:btn-primary={kind === "primary"}
    class:btn-secondary={kind === "secondary"}
    class:btn-danger={kind === "danger"}
    class:btn-ghost={kind === "ghost"}
    class:btn-outline={kind === "outline"}
    class:btn-sm={size === "sm"}
    class:btn-md={size === "md"}
    class:btn-lg={size === "lg"}
    type={type}
    tabindex="0"
    disabled={disabled}
    on:click
    {...$$restProps}
>
    <slot></slot>
</button>
{{< /code >}}

{{<code language="typescript" title="Button.svelte.d.ts">}}
import { SvelteComponentTyped } from "svelte";

export interface ButtonProps
    extends svelte.JSX.HTMLAttributes<HTMLElementTagNameMap["button"]> {
        disabled: boolean;
        kind: "primary" | "danger" | "secondary" | "ghost" | "outline";
        size: "sm" | "md" | "lg";
        type: string = "button";
};

export default class Button extends SvelteComponentTyped< ButtonProps> {};
{{< /code >}}

## Typed Components with Custom Events

{{<code language="tsx" title="Button.svelte">}}
<script lang="ts">
    // ....code

    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    const example: ExampleType = {
        name: 'hello'
    };
</script>

<button
    // ....code
    {...$$restProps}
    on:click
    on:click={() => {
        // make sure to dispatch the regular on:click event
        dispatch("click");
        // dispatch your custom click event
        dispatch("custom-click", example);
    }}
>
    <slot></slot>
</button>
{{< /code >}}

{{<code language="typescript" title="Button.svelte.d.ts">}}
import { SvelteComponentTyped } from "svelte";

export interface ExampleType {
    name: string;
}

// ...ButtonProps code here...

export default class Button extends SvelteComponentTyped<
    ButtonProps,
    {
        // click:custom-click: your custom event
        // ExampleType: the type of the data sent
        ["click:custom-click"]: ExampleType
    }
> {};
{{< /code >}}

Outer Component



{{<code language="tsx" title="Outer.svelte">}}
<script lang="ts">
    import Button from "./Button.svelte";
</script>

<Button
    on:click:custom-click={(e: CustomEvent<ExampleType>) => {
        console.log(e.detail);      // data from event dispatched
    }}
>Click me!</Button>

{{< /code >}}
