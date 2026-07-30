<script lang="ts">
	import Navbar from '$lib/components/dashboard/Navbar.svelte';
	import type { LayoutProps } from './$types';

let { data, children }: LayoutProps = $props();
</script>

<Navbar branchName={data.branchName} alarmCount={data.alarmCount} userImage={data.userImage} />

{@render children()}
