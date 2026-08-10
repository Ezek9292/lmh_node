<script lang="ts">
	import { onDestroy } from 'svelte';
	import checkIcon from '$lib/assets/check_circle.svg';
	import contentCopyIcon from '$lib/assets/content_copy.svg';
	import reloadIcon from '$lib/assets/reload.svg';
	import PageHeader from '$lib/components/dashboard/PageHeader.svelte';

	type CredentialId = 'egs-id' | 'authorization-id' | 'authorization-key';

	interface Credential {
		id: CredentialId;
		label: string;
		value: string;
		secret?: boolean;
	}

	const egsId = 'Lorem ipsum dolor sit';
	let authorizationId = $state('Lorem ipsum dolor sit');
	let authorizationKey = $state('my-secret-key');
	let copiedCredential = $state<CredentialId | null>(null);
	let statusMessage = $state('');
	let copyResetTimer: ReturnType<typeof setTimeout> | undefined;

	const credentials = $derived<Credential[]>([
		{ id: 'egs-id', label: 'EGS ID', value: egsId },
		{
			id: 'authorization-id',
			label: 'EGS API access authorization ID',
			value: authorizationId
		},
		{
			id: 'authorization-key',
			label: 'EGS API access authorization key',
			value: authorizationKey,
			secret: true
		}
	]);

	async function copyCredential(credential: Credential) {
		try {
			await navigator.clipboard.writeText(credential.value);
			copiedCredential = credential.id;
			statusMessage = `${credential.label} copied to clipboard.`;

			clearTimeout(copyResetTimer);
			copyResetTimer = setTimeout(() => {
				copiedCredential = null;
			}, 1500);
		} catch (error) {
			console.error('Unable to copy credential:', error);
			statusMessage = 'Unable to copy this credential. Please copy it manually.';
		}
	}

	function regenerateAuthorization() {
		authorizationId = crypto.randomUUID();
		authorizationKey = crypto.randomUUID();
		copiedCredential = null;
		statusMessage = 'API authorization credentials regenerated.';
	}

	onDestroy(() => clearTimeout(copyResetTimer));
</script>

<svelte:head>
	<title>API Access</title>
	<meta name="description" content="View and regenerate EGS API access credentials." />
</svelte:head>

<section class="api-page">
	<div class="api-container">
		<PageHeader title="API" />

		<p class="sr-only" role="status" aria-live="polite">{statusMessage}</p>

		<section class="api-section" aria-labelledby="api-credentials-heading">
			<header class="section-header">
				<h2 id="api-credentials-heading">API access credential</h2>

				<button class="regenerate-button" type="button" onclick={regenerateAuthorization}>
					<img src={reloadIcon} alt="" />
					Re-generate authorization
				</button>
			</header>

			<div class="credentials-grid">
				{#each credentials as credential (credential.id)}
					<article class="credential">
						<div class="credential-heading">
							<h3>{credential.label}</h3>

							<button
								class="copy-button"
								type="button"
								aria-label={`Copy ${credential.label}`}
								title={`Copy ${credential.label}`}
								onclick={() => copyCredential(credential)}
							>
								<img
									src={copiedCredential === credential.id ? checkIcon : contentCopyIcon}
									alt=""
								/>
							</button>
						</div>

						<p class:secret-value={credential.secret}>
							{credential.secret ? '**********' : credential.value}
						</p>
					</article>
				{/each}
			</div>
		</section>
	</div>
</section>

<style>
	:global(*) {
		box-sizing: border-box;
	}

	.api-page {
		min-height: calc(100vh - 45px);
		padding: 32px 20px 80px;
		background: #f6f6f6;
		font-family: 'Roboto', 'Helvetica Neue', Helvetica, Arial, sans-serif;
	}

	.api-container {
		width: min(1160px, 100%);
		margin: 0 auto;
	}

	.api-section {
		background: #ffffff;
	}

	.section-header {
		display: flex;
		align-items: center;
		justify-content: space-between;
		min-height: 45px;
		padding: 7px 16px;
		border-bottom: 1px solid #dddddd;
	}

	.section-header h2 {
		margin: 0;
		font-size: 11px;
		font-weight: 600;
		letter-spacing: 0.05em;
		text-transform: uppercase;
	}

	.regenerate-button {
		display: inline-flex;
		gap: 6px;
		align-items: center;
		justify-content: center;
		min-height: 29px;
		padding: 5px 12px;
		color: #1f2a16;
		font-size: 11px;
		font-weight: 500;
		text-transform: uppercase;
		background: #8ac926;
		border: 1px solid #8ac926;
		cursor: pointer;
	}

	.regenerate-button:hover {
		background: #79b91d;
	}

	.regenerate-button img {
		width: 15px;
		height: 15px;
	}

	.credentials-grid {
		display: grid;
		grid-template-columns: repeat(3, minmax(0, 1fr));
	}

	.credential {
		min-width: 0;
		padding: 14px 16px 17px;
		border-right: 1px solid #eeeeee;
	}

	.credential:last-child {
		border-right: none;
	}

	.credential-heading {
		display: flex;
		gap: 10px;
		align-items: center;
		justify-content: space-between;
	}

	.credential-heading h3 {
		overflow: hidden;
		margin: 0;
		font-size: 11px;
		font-weight: 500;
		letter-spacing: 0.05em;
		text-overflow: ellipsis;
		text-transform: uppercase;
		white-space: nowrap;
	}

	.credential > p {
		overflow: hidden;
		margin: 5px 0 0;
		font-size: clamp(18px, 2vw, 27px);
		font-weight: 400;
		line-height: 1.05;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.credential > p.secret-value {
		font-size: 35px;
		letter-spacing: 1px;
	}

	.copy-button {
		display: grid;
		width: 20px;
		height: 20px;
		flex-shrink: 0;
		padding: 2px;
		place-items: center;
		background: transparent;
		border: none;
		cursor: pointer;
	}

	.copy-button img {
		display: block;
		width: 16px;
		height: 16px;
	}

	.copy-button:focus-visible,
	.regenerate-button:focus-visible {
		outline: 2px solid #222222;
		outline-offset: 2px;
	}

	.sr-only {
		position: absolute;
		width: 1px;
		height: 1px;
		padding: 0;
		margin: -1px;
		overflow: hidden;
		clip: rect(0, 0, 0, 0);
		white-space: nowrap;
		border: 0;
	}

	@media (max-width: 800px) {
		.credentials-grid {
			grid-template-columns: 1fr;
		}

		.credential {
			border-right: none;
			border-bottom: 1px solid #eeeeee;
		}

		.credential:last-child {
			border-bottom: none;
		}

		.credential > p {
			font-size: 20px;
		}
	}

	@media (max-width: 480px) {
		.api-page {
			padding: 20px 10px 60px;
		}

		.section-header {
			align-items: stretch;
			flex-direction: column;
			gap: 10px;
			padding: 12px;
		}
	}
</style>
