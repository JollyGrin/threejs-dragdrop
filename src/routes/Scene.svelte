<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { interactivity, OrbitControls, useGltf } from '@threlte/extras';
	import { Spring } from 'svelte/motion';
	import { writable } from 'svelte/store';

	interactivity();

	const scale = new Spring(1);
	const modelUrl = writable('https://jollygrin.github.io/threejs-decal/model.gltf');

	// Handle file drop
	function handleDrop(event: DragEvent) {
		event.preventDefault();
		const file = event.dataTransfer?.files[0];
		if (file && (file.name.endsWith('.glb') || file.name.endsWith('.gltf'))) {
			const url = URL.createObjectURL(file);
			modelUrl.set(url);
		}
	}

	let rotation = 0;
	useTask((delta) => {
		rotation += delta;
	});
</script>

<svelte:window on:dragover|preventDefault on:drop={handleDrop} />

<T.PerspectiveCamera
	makeDefault
	position={[10, 10, 10]}
	oncreate={(ref) => {
		ref.lookAt(0, 1, 0);
	}}
>
	<OrbitControls />
</T.PerspectiveCamera>

<T.DirectionalLight position={[0, 10, 10]} castShadow />

{#await useGltf($modelUrl)}
	<!-- Loading state -->
	<T.Mesh position.y={1}>
		<T.SphereGeometry args={[0.5, 16, 16]} />
		<T.MeshStandardMaterial color="gray" wireframe />
	</T.Mesh>
{:then gltf}
	<T
		is={gltf.scene}
		rotation.y={rotation}
		position.y={1}
		scale={scale.current}
		onpointerenter={() => {
			scale.target = 1.5;
		}}
		onpointerleave={() => {
			scale.target = 1;
		}}
		castShadow
	/>
{:catch error}
	<!-- Error state -->
	<T.Mesh position.y={1}>
		<T.BoxGeometry args={[1, 1, 1]} />
		<T.MeshStandardMaterial color="red" />
	</T.Mesh>
{/await}
