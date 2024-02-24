<script lang="ts">
	import { Bitski, AuthenticationStatus } from 'bitski';
	import type { EIP1193ProviderWithoutEvents } from 'eip-1193';
	import { onMount } from 'svelte';
	const bitski = new Bitski(
		'9a75db9c-aa11-4151-bf02-5018eb51c9bc',
		'http://localhost:5174/bitski.html'
	);

	let provider: EIP1193ProviderWithoutEvents | undefined;
	let account: `0x${string}` | undefined;

	async function onConnected() {
		provider = bitski.getProvider() as EIP1193ProviderWithoutEvents;
		const accounts = await provider.request({ method: 'eth_requestAccounts' });
		account = accounts[0];
		console.log({ account });
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
</script>

{#if !provider}
	<button on:click={() => connect()}>Connect</button>
{:else}
	<button on:click={() => signMessage()}>sign</button>
	<button on:click={() => disconnect()}>Disconnect</button>
{/if}
