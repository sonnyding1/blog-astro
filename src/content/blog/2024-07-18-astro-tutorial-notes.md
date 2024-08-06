---
title: Astro tutorial notes
pubDatetime: 2024-07-18
postSlug: astro-tutorial-notes
featured: false
draft: false
tags:
  - astro
  - tldr
description: This blog post is a TL;DR version of the official Astro tutorial.
---
This blog post is a TL;DR version of the [official Astro tutorial](https://docs.astro.build/en/tutorial).

## Routes

Routes are handled by Astro. Different routes are under `src/pages/`, `index.astro` points to `/`.

Instead of writing HTML with `.astro`, you can write Markdown files under `src/pages/` as well, and Astro will render the contents correctly. So Astro converts Markdown to HTML under the hood.

Hmm... So far it seems to me that Astro is very good for writing static sites like blog sites.

## Writing JS

```astro
---

---
```

The thing above is **code fence**, you can put JS inside.

## CSS

Pass variables in CSS style like `define:vars={{skillColor}}`, then use the variables inside like `var(--skillColor)`.

## Components

Suppose we have `Navigation.astro`, you can import it into another file and use as a component like this: `<Navigation />`.

You can also pass props into components:

```astro
const { platform, username } = Astro.props;
```

```astro
<Social platform="twitter" username="astrodotbuild" />
```

## Layouts

It is also possible to use slot `<slot />`, just like Vue. So you can make layouts with it.

To apply layouts to Markdown files, add to frontmatter:

```astro
layout: ../../layouts/MarkdownPostLayout.astro
```

You can nest layouts.

## Astro API

### `Astro.glob`

Fetch Markdown files using `Astro.glob`:

```astro
const allPosts = await Astro.glob('./posts/*.md');
```

### `getStaticPaths()`

`getStaticPaths()` is a function that returns an array of page routes. Here is an example usage to produce dynamic tag pages in file `src/pages/tags/[tag].astro`:

```astro
export async function getStaticPaths() {
  const allPosts = await Astro.glob('../posts/*.md');
  const uniqueTags = [...new Set(allPosts.map((post) => post.frontmatter.tags).flat())];

  return uniqueTags.map((tag) => {
    const filteredPosts = allPosts.filter((post) => post.frontmatter.tags.includes(tag));
    return {
    params: { tag },
    props: { posts: filteredPosts },
    };
  });
}
```

In which, the key takeaway is we return an object with URL params and props like

```astro
[
  {params: {tag: "astro"}, props: {posts: allPosts}},
  {params: {tag: "successes"}, props: {posts: allPosts}},
  {params: {tag: "community"}, props: {posts: allPosts}},
  {params: {tag: "blogging"}, props: {posts: allPosts}},
  {params: {tag: "setbacks"}, props: {posts: allPosts}},
  {params: {tag: "learning in public"}, props: {posts: allPosts}}
]
```

## Astro Islands

Client directives such as `client:load` means that the resource specified will be **hydrated island**, which means JS code is sent to the client. An example usage is 

```astro
<Greeting client:load messages={["Hej", "Hallo", "Hola", "Habari"]} />
```

Basically you need hydration when you want a dynamic component like an interactive counter.

## Conclusion

It looks like Astro really leans toward building static websites, I would imagine myself writing a blog site very easily using Astro. This doesn't mean I cannot make dynamic websites though, I could use Astro Islands, possibly incorporating other frameworks, but it won't be nearly as easy as making static websites.