# Threlte GLTF Drag & Drop Example

This project demonstrates how to implement drag and drop functionality for GLTF/GLB models in a Threlte application. It shows how to load 3D models dynamically and replace them at runtime using the browser's drag and drop API.

## Key Features

- Initial GLTF model loading
- Drag and drop support for .gltf and .glb files
- Loading state indicator
- Error handling
- Model animation (rotation)
- Interactive hover effects

## Implementation Guide

### 1. Required Dependencies

```bash
npm install @threlte/core @threlte/extras three
```

### 2. Core Components

The main functionality is implemented in `Scene.svelte`:

```svelte
import { T, useTask } from '@threlte/core';
import { interactivity, OrbitControls, useGltf } from '@threlte/extras';
import { Spring } from 'svelte/motion';
import { writable } from 'svelte/store';
```

### 3. Model URL Store

Create a writable store to manage the current model URL:

```svelte
const modelUrl = writable('path/to/initial/model.gltf');
```

### 4. Drag and Drop Handler

Implement the drag and drop functionality:

```svelte
function handleDrop(event: DragEvent) {
    event.preventDefault();
    const file = event.dataTransfer?.files[0];
    if (file && (file.name.endsWith('.glb') || file.name.endsWith('.gltf'))) {
        const url = URL.createObjectURL(file);
        modelUrl.set(url);
    }
}
```

Add the event listeners to your window:

```svelte
<svelte:window on:dragover|preventDefault on:drop={handleDrop} />
```

### 5. Loading the GLTF Model

Use Threlte's `useGltf` hook to load the model:

```svelte
{#await useGltf($modelUrl)}
    <!-- Loading state -->
    <T.Mesh>
        <T.SphereGeometry args={[0.5, 16, 16]} />
        <T.MeshStandardMaterial color="gray" wireframe />
    </T.Mesh>
{:then gltf}
    <T
        is={gltf.scene}
        rotation.y={rotation}
        position.y={1}
        scale={scale.current}
    />
{:catch error}
    <!-- Error state -->
    <T.Mesh>
        <T.BoxGeometry args={[1, 1, 1]} />
        <T.MeshStandardMaterial color="red" />
    </T.Mesh>
{/await}
```

## Key Concepts

1. **GLTF Loading**: Using `useGltf` from `@threlte/extras` for efficient model loading
2. **State Management**: Using Svelte's stores to manage the current model URL
3. **File Handling**: Using `URL.createObjectURL()` to create local URLs for dropped files
4. **Loading States**: Showing loading and error states while handling model changes
5. **Interactivity**: Using Threlte's built-in interactivity system for hover effects

## Best Practices

1. Always provide loading and error states for better user experience
2. Clean up object URLs when they're no longer needed to prevent memory leaks
3. Validate file types before attempting to load them
4. Use Threlte's built-in components (`<T>`) instead of raw Three.js objects
5. Leverage Svelte's reactive declarations for dynamic updates

## Tips

- The model's scale, position, and rotation can be adjusted through props on the `<T>` component
- Use `OrbitControls` for camera manipulation
- Consider adding loading progress indicators for large models
- Add file size limits and type validation for production use

## Resources

- [Threlte Documentation](https://threlte.xyz)
- [Three.js Documentation](https://threejs.org)
- [GLTF Specification](https://www.khronos.org/gltf/)

## License

MIT
