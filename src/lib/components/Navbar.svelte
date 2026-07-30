<script lang="ts">
	import { resolve } from '$app/paths';
	import signalBarsIcon from '$lib/assets/signal-bars.svg';
	import connectionIcon from '$lib/assets/connection.svg';
	import logo from '$lib/assets/logo.jpg';
	import userProfile from '$lib/assets/user-profile.jpg';
	import settingsIcon from '$lib/assets/settings.svg';
	import dropdown from '$lib/assets/dropdown.svg'

	interface Props {
		branchName: string;
		alarmCount?: number;
		userImage?: string | null;
	}

	let { branchName, alarmCount = 0, userImage }: Props = $props();
</script>

<header class="navbar">
	<div class="left-section">
		<a class="logo" href={resolve('/dashboard')} aria-label="Dashboard home">
			<img src={logo} alt="LMH logo" />
		</a>

		<button class="profile-button" type="button">
			{#if userImage}
				<img src={userProfile} alt="User profile" />
			{:else}
				<span class="avatar-placeholder">ED</span>
			{/if}

			<img src={dropdown} alt="little dropdown">
		</button>

		<p class="branch-name">{branchName}</p>
	</div>

	<nav class="center-section" aria-label="Dashboard navigation">
		<a class="icon-link" href={resolve('/dashboard')} aria-label="Dashboard overview">
			<img class="signal-icon" src={signalBarsIcon} alt="" />
		</a>

		{#if alarmCount > 0}
			<button class="alarm" type="button" aria-label={`${alarmCount} errors and alarms`}>
				<span class="alarm-count">{alarmCount}</span>
				<span>ERROR & ALARM</span>
			</button>
		{/if}

		<button class="connection-button" type="button" aria-label="Connections">
			<img src={connectionIcon} alt="" />
		</button>
	</nav>

	<div class="right-section">
		<button class="action-link" type="button">
			<span>≡</span>
			<span>INTEGRATION</span>
		</button>

		<button class="action-link" type="button">
			<span>≡</span>
			<span>EXPORT</span>
		</button>

		<button class="action-link" type="button">
			<span>⌘</span>
			<span>API</span>
		</button>

		<button class="settings" type="button" aria-label="Settings">
			<img src={settingsIcon} alt="" />
		</button>
	</div>
</header>

<style>
	.navbar {
		position: sticky;
		top: 0;
		z-index: 100;

		display: grid;
		grid-template-columns: 1fr auto 1fr;
		align-items: center;
		gap: 1rem;

		width: auto;
		height: 0px;
		min-height: 45px;
		padding: 0 1rem;

		background: white;
		box-shadow: 0 2px 8px rgb(0 0 0 / 8%);
	}

	.left-section,
	.center-section,
	.right-section {
		display: flex;
		align-items: center;
	}

	.left-section {
		gap: 1rem;
	}

	.center-section {
		justify-content: center;
		gap: 2rem;
	}

	.right-section {
		justify-content: flex-end;
		gap: 0.5rem;
	}

	.logo img {
		display: block;
		width: 1.8rem;
		height: 1.8rem;
		object-fit: contain;
	}

	.profile-button {
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 0;
		border: none;
		background: transparent;
		cursor: pointer;
	}

	.profile-button img,
	.avatar-placeholder {
		width: 32px;
		height: 32px;
		border-radius: 50%;
	}

	.profile-button img {
		object-fit: cover;
	}

	.avatar-placeholder {
		display: grid;
		place-items: center;
		color: white;
		background: #334155;
		font-size: 0.75rem;
		font-weight: 700;
	}

	.branch-name {
		margin: 0;
		font-size: 0.8rem;
		white-space: nowrap;
		font-family:Arial, Helvetica, sans-serif
	}

	.icon-link,
	.settings {
		color: #171717;
		text-decoration: none;
	}

	.icon-link,
	.settings {
		display: grid;
		place-items: center;
		border: 0;
		background: transparent;
		cursor: pointer;
	}

	.icon-link {
		width: 2rem;
		height: 2rem;
	}

	.signal-icon {
		display: block;
		width: 1.5rem;
		height: 1.5rem;
	}

	.alarm {
		display: flex;
		align-items: center;
		gap: 0.35rem;

		padding: 0.3rem 0.8rem;
		border: 1px solid #b8b8b8;
		border-radius: 999px;

		font-size: 0.65rem;
		font-weight: 600;
	}

	.alarm-count {
		color: #dc2626;
	}

	.action-link {
		display: flex;
		align-items: center;
		gap: 0.3rem;

		padding: 0.4rem 0.6rem;
		border: 1px solid #8acb45;
		border-radius: 3px;

		color: #333333;
		background: white;
		text-decoration: none;
		font-size: 0.65rem;
		cursor: pointer;
	}

	.connection-button {
		display: grid;
		place-items: center;
		width: 2rem;
		height: 2rem;
		padding: 0;
		border: 0;
		background: transparent;
		cursor: pointer;
	}

	.connection-button img {
		display: block;
		width: 1.35rem;
		height: 1.35rem;
	}

	.settings {
		margin-left: 1.5rem;
		width: 2rem;
		height: 2rem;
		padding: 0;
	}

	.settings img {
		display: block;
		width: 1.25rem;
		height: 1.25rem;
	}

	@media (max-width: 900px) {
		.navbar {
			grid-template-columns: 1fr auto;
		}

		.center-section {
			display: none;
		}

		.branch-name {
			display: none;
		}
	}

	@media (max-width: 600px) {
		.action-link {
			display: none;
		}

		.navbar {
			padding: 0 1rem;
		}
	}
</style>
