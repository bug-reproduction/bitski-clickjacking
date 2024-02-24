<script lang="ts">
	import { createBitskiProvider } from 'bitski-provider';
	function connect() {
		const provider = createBitskiProvider({
			clientId: '9a75db9c-aa11-4151-bf02-5018eb51c9bc',
			signerMethod: 'popup'
		});
		// provider.request({method: 'eth_requestAccounts'})
	}
</script>

<button on:click={() => connect()}>Test</button>
