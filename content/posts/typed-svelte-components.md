+++
title = "Adding Types To Svelte Components With Typescript"
date = "2022-03-30T15:47:03-07:00"
author = "Amy Dang"
authorTwitter = "" #do not include @
cover = ""
tags = ["svelte", "typescript", "webdev"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
draft = true
+++

Use `Button.d.svelte`.

Given a Button svelte component.
**Example Button.svelte**:

{{< code language="svelte" title="Buttons.svelte" id="1" expand="Show" collapse="Hide" isCollapsed="false" >}}
<script lang="ts">
    export let rounded: boolean = false;
    export let disabled: boolean = false;
    export let kind: "primary" 
        | "danger" 
        | "secondary" 
        | "ghost" 
        | "outline"  = "primary";
    export let btnStyle: "default" | "pill" = "default";
    export let href: string = undefined;
    export let size: "sm" | "md" | "lg" | "full" = "md";
    export let width: "full" | "none" = "none";
    export let shadow: boolean = false;
    export let type: string = "button";
</script>

<button
    class:btn={true}
    class:focus-outline={true}
    class:btn-disabled={disabled}
    class:btn-primary={kind === "primary"}
    class:btn-secondary={kind === "secondary"}
    class:btn-danger={kind === "danger"}
    class:btn-ghost={kind === "ghost"}
    class:btn-outline={kind === "outline"}
    class:btn-pill={btnStyle === "pill"}
    class:btn-sm={size === "sm"}
    class:btn-md={size === "md"}
    class:btn-lg={size === "lg"}
    class:btn-full={width === "full"}
    class:btn-shadow={shadow}
    {type}
    tabindex="0"
    {disabled}
    on:click
    {...$$restProps}
>
    <slot></slot>
</button>
{{< /code >}}

