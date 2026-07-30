<script lang="ts">
	import Navbar from '$lib/components/Navbar.svelte';
	import type { LayoutProps } from './$types';

	let { data, children }: LayoutProps = $props();
</script>

<div class="dashboard-layout">
	<Navbar branchName={data.branchName} alarmCount={data.alarmCount} userImage={data.userImage} />

	<main class="dashboard-content">
		{@render children()}
	</main>
</div>

<style>
	.dashboard-layout {
		min-height: 100vh;
		padding: 0rem 0.9rem 0;
		background: #f4f4f4;
	}

	.dashboard-content {
		padding: 2rem 0.25rem 1.5rem;
	}
</style>
