<script lang="ts">
	import { onMount, setContext } from "svelte";
	import { writable } from "svelte/store";

	const userID = writable(0);
	let isDropDownOpen = false;
  setContext('user_id', userID);

	onMount(() => {
		fetch("http://localhost:4000/api/profile", {
			credentials: "include"
		})
			.then((data) => data.json())
			.then((data) => {
				if (data && data.id) {
					userID.set(data.id);
				}
			});
	});

</script>

<nav>
	<a href="/">home</a>
	{#if $userID != 0}
		<button
			on:click={() => {
				isDropDownOpen = !isDropDownOpen;
			}}>profile</button
		>
		{#if isDropDownOpen}
			<div class="dropdown">
				<div class="dropdown-text">
					<button>logout</button>
					<button>view full profile</button>
				</div>
			</div>
		{/if}
	{:else}
		<a href="/login">login</a>
		<a href="/signup">signup</a>
	{/if}
</nav>
<slot />

<style>
	nav {
		padding: 1rem;
		background-color: #000;
		color: white;
		display: flex;
		flex-direction: flex-start;
		gap: 2rem;
	}
	nav > a {
		color: inherit;
		text-decoration: none;
	}

	.dropdown {
		position: relative;
		background-color: black;
	}
	.dropdown-text {
		position: absolute;
		background-color: inherit;
		padding: 1rem;
	}
</style>
