<script lang="ts">
	import { resolve } from '$app/paths';

	interface Props {
		title: string;
	}

	let { title }: Props = $props();
</script>

<header class="page-header">
	<a href={resolve('/dashboard')} class="back-link">
		<span aria-hidden="true">←</span>
		Back
	</a>
	<span class="header-divider" aria-hidden="true"></span>
	<h1>{title}</h1>
</header>

<style>
	.page-header {
		display: flex;
		align-items: center;
		min-height: 48px;
		padding: 0 16px;
		margin-bottom: 8px;
		background: #ffffff;
		border-bottom: 1px solid #eeeeee;
	}

	h1 {
		margin: 0;
		font-size: 13px;
		font-weight: 600;
		letter-spacing: 0.04em;
		text-transform: uppercase;
	}

	.back-link {
		display: inline-flex;
		gap: 6px;
		align-items: center;
		color: #8ac926;
		font-size: 12px;
		font-weight: 700;
		letter-spacing: 0.04em;
		text-decoration: none;
		text-transform: uppercase;
	}

	.back-link:hover {
		text-decoration: underline;
	}

	.back-link:focus-visible {
		outline: 2px solid #222222;
		outline-offset: 2px;
	}

	.header-divider {
		width: 1px;
		height: 17px;
		margin: 0 17px;
		background: #d9d9d9;
	}

	@media (max-width: 480px) {
		.page-header {
			padding: 0 12px;
		}
	}
</style>
