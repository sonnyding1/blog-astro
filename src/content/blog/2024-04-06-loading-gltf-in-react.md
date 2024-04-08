---
title: 3D fox in React
pubDatetime: 2024-04-06
postSlug: 3d-fox-in-react
featured: false
draft: true
tags:
  - project
  - front-end
  - 3D
description: Loading glTF fox model in React that runs around.
---
under construction...

Following my previous post about implementing a CMS for my portfolio web application, I have also added a 3D scene in the background that included a fox. The fox would run around, following the uesr's cursor.

![fox-demo]()

## Setting up the 3D scene

Since my project is using React, I chose to use [React Three Fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction) and [Drei](https://github.com/pmndrs/drei).

```tsx
<div className="fixed top-0 left-0 w-screen h-screen -z-10">
  <Canvas>
    <Fox />
  </Canvas>
</div>
```

I placed this code snippet at the same level as my landing page elements. I gave my Canvas for the 3D scene the dimension of the entire screen, and made sure that it is not affected by scrolling, and is hidden in the background so it won't prevent normal clicking events.

## Loading glTF model

I used an open source model from this [GitHub repo](https://github.com/KhronosGroup/glTF-Sample-Models/tree/main/2.0/Fox). The model already includes the vertices, materials, animations and so on, all I had to do was to load it into my project.

```tsx
export default function Fox() {
  const gltf = useLoader(GLTFLoader, "/fox_gltf/Fox.gltf");

  return (
    <primitive
      object={gltf.scene}
    />
  );
}
```

The above approach essentially bulk loads the entire fox scene, instead of unpacking the glTF file, and then load them step by step. Very convenient.

## Perspective

