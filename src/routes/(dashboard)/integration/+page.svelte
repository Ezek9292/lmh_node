<script lang="ts">
	import { resolve } from '$app/paths';
	import contentCopyIcon from '$lib/assets/content_copy.svg';
	import checkIcon from '$lib/assets/check_circle.svg';

	interface IntegrationParameter {
		label: string;
		value: string;
		secret: boolean;
	}

	interface Integration {
		id: string;
		name: string;
		description: string;
		parameters: IntegrationParameter[];
	}

	let integrations = $state<Integration[]>([
		{
			id: 'smtp',
			name: 'SMTP',
			description: 'Update your SMTP integration settings.',
			parameters: [
				{
					label: 'Integration Parameter 1',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 2',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 3',
					value: 'smtp-secret-key',
					secret: true
				}
			]
		},
		{
			id: 'better-stack',
			name: 'Better Stack',
			description: 'Update your Better Stack integration settings.',
			parameters: [
				{
					label: 'Integration Parameter 1',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 2',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 3',
					value: 'better-stack-key',
					secret: true
				}
			]
		},
		{
			id: 'opsgenie',
			name: 'Opsgenie',
			description: 'Update your Opsgenie integration settings.',
			parameters: [
				{
					label: 'Integration Parameter 1',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 2',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 3',
					value: 'opsgenie-secret-key',
					secret: true
				}
			]
		},
		{
			id: 'pagerduty',
			name: 'PagerDuty',
			description: 'Update your PagerDuty integration settings.',
			parameters: [
				{
					label: 'Integration Parameter 1',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 2',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 3',
					value: 'pagerduty-secret-key',
					secret: true
				}
			]
		},
		{
			id: 'servicenow',
			name: 'ServiceNow',
			description: 'Update your ServiceNow integration settings.',
			parameters: [
				{
					label: 'Integration Parameter 1',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 2',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 3',
					value: 'servicenow-secret-key',
					secret: true
				}
			]
		},
		{
			id: 'slack',
			name: 'Slack',
			description: 'Update your Slack integration settings.',
			parameters: [
				{
					label: 'Integration Parameter 1',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 2',
					value: 'Lorem ipsum dolor sit',
					secret: false
				},
				{
					label: 'Integration Parameter 3',
					value: 'slack-secret-key',
					secret: true
				}
			]
		}
	]);

	let selectedIntegration = $state<Integration | null>(null);

	let formData = $state({
		parameterOne: '',
		parameterTwo: '',
		parameterThree: ''
	});

	let copiedText = $state('');
	let statusMessage = $state('');

	function openUpdateModal(integration: Integration) {
		selectedIntegration = integration;

		formData = {
			parameterOne: integration.parameters[0]?.value ?? '',
			parameterTwo: integration.parameters[1]?.value ?? '',
			parameterThree: integration.parameters[2]?.value ?? ''
		};

		statusMessage = '';
	}

	function closeUpdateModal() {
		selectedIntegration = null;
	}

	function updateIntegration(event: SubmitEvent) {
		event.preventDefault();

		if (!selectedIntegration) {
			return;
		}

		const integrationId = selectedIntegration.id;
		const integrationName = selectedIntegration.name;

		const newValues = [formData.parameterOne, formData.parameterTwo, formData.parameterThree];

		integrations = integrations.map((integration) => {
			if (integration.id !== integrationId) {
				return integration;
			}

			return {
				...integration,
				parameters: integration.parameters.map((parameter, index) => ({
					...parameter,
					value: newValues[index]
				}))
			};
		});

		statusMessage = `${integrationName} parameters updated successfully.`;

		closeUpdateModal();
	}

	async function copyParameter(value: string, label: string) {
		try {
			await navigator.clipboard.writeText(value);

			copiedText = label;

			setTimeout(() => {
				copiedText = '';
			}, 1500);
		} catch (error) {
			console.error('Unable to copy parameter:', error);
			statusMessage = 'Unable to copy this value. Please copy it manually.';
		}
	}

	function handleBackdropClick(event: MouseEvent) {
		if (event.target === event.currentTarget) {
			closeUpdateModal();
		}
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Escape' && selectedIntegration) {
			closeUpdateModal();
		}
	}
</script>

<svelte:window onkeydown={handleKeydown} />

<svelte:head>
	<title>Integration Settings</title>
	<meta
		name="description"
		content="Manage SMTP, Better Stack, Opsgenie, PagerDuty, ServiceNow and Slack integrations."
	/>
</svelte:head>

<section class="integration-page">
	<div class="integration-container">
		<header class="page-header">
			<a href={resolve('/dashboard')} class="back-link">
				<span aria-hidden="true">←</span>
				Back
			</a>

			<span class="header-divider"></span>

			<h1>Integration</h1>
		</header>

		{#if statusMessage}
			<div class="success-message" role="status">
				{statusMessage}
			</div>
		{/if}

		<div class="integrations-list">
			{#each integrations as integration (integration.id)}
				<article class="integration-card">
					<header class="integration-header">
						<h2>{integration.name}</h2>

						<button
							type="button"
							class="update-button"
							onclick={() => openUpdateModal(integration)}
						>
							<img src="{checkIcon}" alt="checksign icon" class="update-icon" />
							Update
						</button>
					</header>

					<div class="parameters-grid">
						{#each integration.parameters as parameter (parameter.label)}
							<div class="parameter">
								<div class="parameter-top">
									<span class="parameter-label">
										{parameter.label}
									</span>

									<button
										type="button"
										class="copy-button"
										aria-label={`Copy ${parameter.label}`}
										title={`Copy ${parameter.label}`}
										onclick={() =>
											copyParameter(parameter.value, `${integration.id}-${parameter.label}`)}
									>
										{#if copiedText === `${integration.id}-${parameter.label}`}
											✓
										{:else}
											<img class="copy-icon" src={contentCopyIcon} alt="" />
										{/if}
									</button>
								</div>

								<p class:secret-value={parameter.secret}>
									{parameter.secret ? '••••••••' : parameter.value}
								</p>
							</div>
						{/each}
					</div>
				</article>
			{/each}
		</div>
	</div>
</section>

{#if selectedIntegration}
	<div class="modal-backdrop" role="presentation" onclick={handleBackdropClick}>
		<div class="modal" role="dialog" aria-modal="true" aria-labelledby="modal-title">
			<header class="modal-header">
				<div>
					<p class="modal-eyebrow">
						{selectedIntegration.name} parameter(s) update
					</p>

					<h2 id="modal-title">
						Update {selectedIntegration.name} parameter(s)
					</h2>
				</div>

				<button
					type="button"
					class="close-button"
					aria-label="Close modal"
					onclick={closeUpdateModal}
				>
					×
				</button>
			</header>

			<form class="modal-form" onsubmit={updateIntegration}>
				<label>
					<span>Integration Parameter 1</span>

					<input
						type="text"
						name="parameterOne"
						bind:value={formData.parameterOne}
						placeholder="Integration Parameter 1"
						required
					/>

					<small> Enter the first integration configuration value. </small>
				</label>

				<label>
					<span>Integration Parameter 2</span>

					<input
						type="text"
						name="parameterTwo"
						bind:value={formData.parameterTwo}
						placeholder="Integration Parameter 2"
						required
					/>

					<small> Enter the second integration configuration value. </small>
				</label>

				<label>
					<span>Integration Parameter 3</span>

					<input
						type="password"
						name="parameterThree"
						bind:value={formData.parameterThree}
						placeholder="Integration Parameter 3"
						required
					/>

					<small> Enter the secret key or authentication token. </small>
				</label>

				<button type="submit" class="proceed-button">
					<img src="{checkIcon}" alt="checksign icon" class="update-icon" />
					Proceed
				</button>
			</form>
		</div>
	</div>
{/if}

<style>
	:global(*) {
		box-sizing: border-box;
	}

	.integration-page {
		min-height: calc(100vh - 70px);
		padding: 32px 20px 80px;
		background: #f6f6f6;
		font-family: Arial, Helvetica, sans-serif;
	}

	.integration-container {
		width: min(1160px, 100%);
		margin: 0 auto;
	}

	.page-header {
		display: flex;
		align-items: center;
		min-height: 48px;
		padding: 0 16px;
		margin-bottom: 8px;
		background: #ffffff;
		border-bottom: 1px solid #eeeeee;
	}

	.page-header h1 {
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
		color: #8AC926;
		font-size: 12px;
		font-weight: 700;
		letter-spacing: 0.04em;
		text-decoration: none;
		text-transform: uppercase;
	}

	.back-link:hover {
		text-decoration: underline;
	}

	.header-divider {
		width: 1px;
		height: 17px;
		margin: 0 17px;
		background: #d9d9d9;
	}

	.success-message {
		padding: 12px 16px;
		margin-bottom: 8px;
		color: #8AC926;
		font-size: 13px;
		background: #ebf8d8;
		border: 1px solid #8AC926;
	}

	.integrations-list {
		background: #ffffff;
	}

	.integration-card {
		background: #ffffff;
		border-bottom: 8px solid #f6f6f6;
	}

	.integration-header {
		display: flex;
		align-items: center;
		justify-content: space-between;
		min-height: 42px;
		padding: 0 16px;
		border-bottom: 1px solid #dddddd;
	}

	.integration-header h2 {
		margin: 0;
		font-size: 11px;
		font-weight: 600;
		letter-spacing: 0.06em;
		text-transform: uppercase;
	}

	.update-button {
		display: inline-flex;
		gap: 6px;
		align-items: center;
		justify-content: center;
		min-height: 29px;
		padding: 5px 12px;
		color: #1f2a16;
		font-size: 11px;
		font-weight: 600;
		text-transform: uppercase;
		background: #8AC926;
		border: 1px solid #8AC926;
		cursor: pointer;
	}

	.update-button:hover {
		background: #8AC926;
	}

	.update-button:focus-visible,
	.copy-button:focus-visible,
	.close-button:focus-visible,
	.proceed-button:focus-visible,
	.back-link:focus-visible {
		outline: 2px solid #222222;
		outline-offset: 2px;
	}

	.update-icon {
		font-size: 10px;
		color: black;
	}

	.parameters-grid {
		display: grid;
		grid-template-columns: repeat(3, minmax(0, 1fr));
	}

	.parameter {
		min-width: 0;
		padding: 14px 16px 17px;
		border-right: 1px solid #eeeeee;
	}

	.parameter:last-child {
		border-right: none;
	}

	.parameter-top {
		display: flex;
		gap: 10px;
		align-items: center;
		justify-content: space-between;
	}

	.parameter-label {
		overflow: hidden;
		font-size: 11px;
		font-weight: 500;
		letter-spacing: 0.05em;
		text-overflow: ellipsis;
		text-transform: uppercase;
		white-space: nowrap;
	}

	.parameter p {
		overflow: hidden;
		margin: 5px 0 0;
		font-size: clamp(18px, 2vw, 27px);
		font-weight: 400;
		line-height: 1.05;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.parameter p.secret-value {
		font-size: 22px;
		font-weight: 700;
		letter-spacing: 1px;
	}

	.copy-button {
		display: grid;
		width: 20px;
		height: 20px;
		flex-shrink: 0;
		padding: 2px;
		place-items: center;
		color: #444444;
		font-size: 12px;
		background: transparent;
		border: none;
		cursor: pointer;
	}

	.copy-button:hover {
		color: #8AC926;
	}

	.copy-icon {
		display: block;
		width: 16px;
		height: 16px;
	}

	.modal-backdrop {
		position: fixed;
		inset: 0;
		z-index: 1000;
		display: grid;
		place-items: start center;
		padding: 160px 20px 40px;
		background: rgb(0 0 0 / 18%);
		overflow-y: auto;
	}

	.modal {
		width: min(390px, 100%);
		background: #ffffff;
		border: 1px solid #dddddd;
		box-shadow: 0 12px 40px rgb(0 0 0 / 18%);
	}

	.modal-header {
		display: flex;
		align-items: flex-start;
		justify-content: space-between;
		padding: 15px 16px;
		border-bottom: 1px solid #dddddd;
	}

	.modal-eyebrow {
		margin: 0 0 17px;
		font-size: 11px;
		font-weight: 600;
		letter-spacing: 0.03em;
		text-transform: uppercase;
	}

	.modal-header h2 {
		margin: 0;
		font-size: 13px;
		font-weight: 500;
	}

	.close-button {
		padding: 0;
		color: #333333;
		font-size: 23px;
		line-height: 1;
		background: transparent;
		border: none;
		cursor: pointer;
	}

	.modal-form {
		display: grid;
		gap: 14px;
		padding: 16px;
	}

	.modal-form label {
		display: grid;
		gap: 6px;
	}

	.modal-form label > span {
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

	.modal-form input {
		width: 100%;
		min-height: 43px;
		padding: 10px 13px;
		color: #202020;
		background: #ffffff;
		border: 1px solid #d2d2d2;
		outline: none;
	}

	.modal-form input:focus {
		border-color: #80c41c;
		box-shadow: 0 0 0 2px rgb(128 196 28 / 14%);
	}

	.modal-form small {
		font-size: 10px;
		line-height: 1.4;
	}

	.proceed-button {
		display: inline-flex;
		gap: 8px;
		align-items: center;
		justify-content: flex-start;
		width: 100%;
		min-height: 43px;
		padding: 10px 13px;
		color: #222222;
		font-size: 12px;
		font-weight: 600;
		text-transform: uppercase;
		background: #ffffff;
		border: 1px solid #d2d2d2;
		cursor: pointer;
	}

	.proceed-button:hover {
		background: #80c41c;
		border-color: #80c41c;
	}

	@media (max-width: 800px) {
		.parameters-grid {
			grid-template-columns: 1fr;
		}

		.parameter {
			border-right: none;
			border-bottom: 1px solid #eeeeee;
		}

		.parameter:last-child {
			border-bottom: none;
		}

		.parameter p {
			font-size: 20px;
		}

		.modal-backdrop {
			padding-top: 90px;
		}
	}

	@media (max-width: 480px) {
		.integration-page {
			padding: 20px 10px 60px;
		}

		.page-header {
			padding: 0 12px;
		}

		.integration-header {
			padding: 0 12px;
		}

		.parameter {
			padding-right: 12px;
			padding-left: 12px;
		}
	}
</style>
