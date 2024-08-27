---
title: Writing my own CMS
pubDatetime: 2024-03-25T10:58:00-07:00
postSlug: writing-my-own-cms
featured: false
draft: false
tags:
  - front-end
  - project
description: I wrote a portfolio web app with CMS that can parse Markdown posts.
---
A few days ago, I began designing my portfolio web app. I aimed to replicate my blog post system's structure, enabling it to render web pages from a collection of markdown files with appropriate `YAML` metadata.

## Tech Stack

I initialized a React project using [Vite](https://vitejs.dev/). For the UI, I experimented with [NextUI](https://nextui.org/) for the first time alongside [Tailwind CSS](https://tailwindcss.com/). NextUI impressed me with its smooth animations, powered by [Framer Motion](https://www.framer.com/motion/).

## Routing

This is my first time developing a multi-paged React web app. I familiarized myself with [React Router](https://reactrouter.com/en/main) to set up the routes for my portfolio:

```tsx
// src/main.tsx

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
  {
    path: "projects/:projectId",
    element: <Project />,
    loader: projectLoader,
    errorElement: <ErrorPage />,
  },
]);
```

The root page features my self-introduction, 3 featured projects, and all other projects. Clicking on specific projects directs users to `projects/:projectId`, where they can delve deeper into the project details.

I discovered that React Routes allow passing `projectId` as an argument to a loader function, which utilizes this argument to return relevant data for the route. This data can be accessed using the `useLoaderData()` function. I successfully fetched blog post data based on specific IDs and rendered them as web pages.

Below is the loader function I wrote:

```tsx
export async function loader({ params }: LoaderFunctionArgs<Params>) {
  console.log(params.projectId);
  const { title, body } = (await getProjectById(params.projectId || "")) || {
    title: "",
    body: "",
  };
  return { title, body };
}
```

## Implementing CMS

I developed a CMS by utilizing a server-side script to parse all markdown files in a folder and convert them into a single JSON file. The frontend can then call functions to extract useful posts and display them in various formats.

The server-side code is located in `/public/main.ts`, and I included `tsx public/main.ts &&` before `npm run dev` and `npm run build` to ensure that my markdown files or posts are up to date before each build.

## Parsing Markdown

I primarily relied on [React Markdown](https://github.com/remarkjs/react-markdown) for parsing markdown. I defined rendering rules as follows:

```tsx
<Markdown
  components={{
    h1: ({ node, ...props }) => (
      <h1 className="text-3xl font-bold py-8" {...props} />
    ),
    h2: ({ node, ...props }) => (
      <h2 className="text-2xl font-semibold py-6" {...props} />
    ),
    h3: ({ node, ...props }) => (
      <h3 className="text-xl font-semibold py-4" {...props} />
    ),
    ul: ({ node, ...props }) => (
      <ul className="list-disc list-inside" {...props} />
    ),
    ol: ({ node, ...props }) => (
      <ol className="list-decimal list-inside" {...props} />
    ),
    li: ({ node, ...props }) => <li className="py-1" {...props} />,
    code(props) {
      const { children, className, node, ...rest } = props;
      const match = /language-(\w+)/.exec(className || "");
      return match ? (
        <SyntaxHighlighter
          {...rest}
          PreTag="div"
          children={String(children).replace(/\n$/, "")}
          language={match[1]}
          style={dark}
          ref={null} // Add a null ref to fix the type error
        />
      ) : (
        <code {...rest} className={className}>
          {children}
        </code>
      );
    },
  }}
>
  {project.body}
</Markdown>
```

I plan to add more rendering rules in the future.

## Closing Notes

Though the tasks involved in creating this portfolio web app were relatively simple, I found the experience rewarding. I gained insights into frontend web app development and a deeper understanding of CMS implementation. Moving forward, I intend to enrich my portfolio with more content and enhance its visual appeal.

Here is the link to my portfolio page: [https://sonnyding-portfoilio.vercel.app/](https://sonnyding-portfoilio.vercel.app/)