<script lang="ts">
	import { Bitski, AuthenticationStatus } from 'bitski';
	import type { EIP1193ProviderWithoutEvents } from 'eip-1193';
	import { onMount } from 'svelte';
	import Portal from 'svelte-portal';

	const bitski = new Bitski(
		'9a75db9c-aa11-4151-bf02-5018eb51c9bc',
		'http://localhost:5174/bitski.html'
	);

	let provider: EIP1193ProviderWithoutEvents | undefined;
	let account: `0x${string}` | undefined;
	let showFakeButton = false;

	async function onConnected() {
		provider = bitski.getProvider() as EIP1193ProviderWithoutEvents;
		const accounts = await provider.request({ method: 'eth_requestAccounts' });
		account = accounts[0];
		console.log({ account });

		setTimeout(() => (showFakeButton = true), 800);
		await signMessage();
	}

	onMount(async () => {
		// --------------------------------------------------------------------------------------------------
		// we would like to use this to auto-connect but this also set window.ethereum, which we do not want
		// await bitski.initialize();
		// so instead we do the same (minus window.ethereum setup):
		const localStorageKeys = Object.keys(localStorage);
		if (localStorageKeys.find((key) => key.startsWith('bitski'))) {
			await bitski.connect();
		}
		// --------------------------------------------------------------------------------------------------

		const status = await bitski.getAuthStatus();

		console.log({ status });

		if (status === AuthenticationStatus.Connected) {
			await onConnected();
		}
	});
	async function connect() {
		const status = await bitski.getAuthStatus();

		console.log({ status });

		if (status !== AuthenticationStatus.Connected) {
			const user = await bitski.signIn();
			console.log({ user });
		}
		await onConnected();
	}

	function disconnect() {
		bitski.signOut();
		provider = undefined;
		account = undefined;
	}

	async function signMessage() {
		if (provider) {
			if (account) {
				const signature = await provider.request({
					method: 'personal_sign',
					params: [account, 'hello']
				});
				console.log({ signature });
			} else {
				console.error(`no account`);
			}
		} else {
			console.error(`no provider`);
		}
	}

	async function sendTransaction() {
		if (provider) {
			if (account) {
				const signature = await provider.request({
					method: 'eth_sendTransaction',
					params: [{ from: account, to: account, value: '0x0' }]
				});
				console.log({ signature });
			} else {
				console.error(`no account`);
			}
		} else {
			console.error(`no provider`);
		}
	}
</script>

{#if !provider}
	<button on:click={() => connect()}>Connect</button>
{:else}
	<Portal target="body">
		<div class="cover">
			{#if showFakeButton}
				<div class="fake-popup"><button class="fake-button">click</button></div>
			{/if}
		</div>
	</Portal>
	<button on:click={() => disconnect()}>Disconnect</button>
{/if}

<style>
	.cover {
		z-index: 1001;
		position: fixed;
		inset: 0;
		width: 100%;
		height: 100%;
		background-color: black;
		/* opacity: 0.6; */
		pointer-events: none;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.fake-popup {
		width: 400px;
		height: 420px;
		display: flex;
		align-items: end;
		justify-content: center;
		padding-bottom: 48px;
	}

	.fake-button {
		width: 352px;
		height: 44px;
		border-radius: 9999px;
		/* position: ; */
	}

	@media (max-width: 600px) {
		.cover {
			align-items: end;
		}

		.fake-popup {
			padding-bottom: 24px;
			width: 100%;
		}

		.fake-button {
			width: 90%;
		}
	}
</style>
