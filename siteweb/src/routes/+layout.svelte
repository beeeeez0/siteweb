<script lang="ts">
	let { children } = $props();
	import Tui from '$lib/tui.svelte';
	import Nameplate from '$lib/Nameplate.svelte';
</script>

<Nameplate/>
<Tui text="Welcome!" />


{@render children()}
