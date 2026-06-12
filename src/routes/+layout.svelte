<script lang="ts">
	import '@fontsource-variable/newsreader';
	import '@fontsource-variable/inter';
	import '@fontsource-variable/jetbrains-mono';
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';
	import newsreaderWoff2 from '@fontsource-variable/newsreader/files/newsreader-latin-wght-normal.woff2?url';
	import interWoff2 from '@fontsource-variable/inter/files/inter-latin-wght-normal.woff2?url';
	import jetbrainsWoff2 from '@fontsource-variable/jetbrains-mono/files/jetbrains-mono-latin-wght-normal.woff2?url';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<link rel="preload" href={newsreaderWoff2} as="font" type="font/woff2" crossorigin="anonymous" />
	<link rel="preload" href={interWoff2} as="font" type="font/woff2" crossorigin="anonymous" />
	<link rel="preload" href={jetbrainsWoff2} as="font" type="font/woff2" crossorigin="anonymous" />
</svelte:head>
{@render children()}
