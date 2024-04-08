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

## Set up the 3D scene

Since my project is using React, I chose to use [React Three Fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction) and [Drei](https://github.com/pmndrs/drei). React Three Fiber is kind of like a translator that translates our React code into three.js code.

```tsx
<div className="fixed top-0 left-0 w-screen h-screen -z-10">
  <Canvas>
    <Fox />
  </Canvas>
</div>
```

I placed this code snippet at the same level as my landing page elements. I gave my Canvas for the 3D scene the dimension of the entire screen, and made sure that it is not affected by scrolling, and is hidden in the background so it won't prevent normal clicking events.

## Load glTF model

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

A camera may be perspective or orthographic. In our case, I definitely want an orthographic view, but by applying an orthographic view, there is now z-fighting on different parts of my fox model. So there was a compromise, I ended up using a perspective camera with FOV of 1, and tweaked some parameters including my fox's scale, camera's distance from the origin and so on.

```tsx
<Canvas camera={{ position: [0, 0, 20], fov: 1 }}></Canvas>
```

## Project browser coordinates to three.js coordinates

Initially, I naively thought I could just map browsers coordinates to three.js coordinates by using some math:

```tsx
var vec = new Vector3();
vec.set(
  (event.clientX / window.innerWidth) * 2 - 1,
  -(event.clientY / window.innerHeight) * 2 + 1,
  0.5
);
```

And then if I apply some scaling constant, I can have my fox move to precisely where my cursor is at. This is absolutely false, because I am still using an orthographic camera. In reality, by applying the above script, I observed greater error as the distance between the cursor and the center of the screen is further apart. I ended up finding the correct method in this post, and wrote this:

```tsx
useEffect(() => {
  const handleMouseMove = (event: { clientX: number; clientY: number }) => {
    var vec = new Vector3();
    var pos = new Vector3();
    vec.set(
      (event.clientX / window.innerWidth) * 2 - 1,
      -(event.clientY / window.innerHeight) * 2 + 1,
      0.5
    );
    vec.unproject(camera);
    vec.sub(camera.position).normalize();
    var distance = -camera.position.z / vec.z;
    pos.copy(camera.position).add(vec.multiplyScalar(distance));

    setFoxDestination(pos);
  };

  // ...
}, [camera]);
```

Where `camera` is obtained by calling `const { camera } = useThree();`. So, every time the dimension of the camera or the browser changes, we recompute the mapping between the browser coordinates and the three.js coordinates.

