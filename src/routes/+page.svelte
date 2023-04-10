<script lang="ts">
	import { onMount } from "svelte";

	type Chatroom = {
		id: number;
		name: string;
	};

	let chatrooms: Chatroom[] = [];
	let messages: any[] = [];

	// this value is modified in mount
	let room_selected = 0;

	function fetchMessages(room_selected: number) {
		fetch(`http://localhost:4000/api/messages/${room_selected}`)
			.then((res) => res.json())
			.then((data) => (messages = data ?? []))
			.catch((err) => console.error(err));
	}

	$: {
		fetchMessages(room_selected);
	}

	onMount(() => {
		fetch("http://localhost:4000/api/chatrooms")
			.then((res) => res.json())
			.then((data) => {
				chatrooms = data;
				room_selected = data[0].id;
			});
	});
</script>

<main>
	<div class="sidebar">
		{#each chatrooms as room}
			<label>
				<input type="radio" bind:group={room_selected} value={room.id} />
				{room.name}
			</label>
		{/each}
	</div>
	<div class="message-box">
		<h2>list of messages</h2>
		<div>
			{#each messages as message}
				<div>
					{message.user_id} -> {message.body}
				</div>
			{/each}
		</div>
	</div>
</main>

<style>
	main {
		display: grid;
		grid-template-columns: 1fr 5fr;
		height: calc(100vh - 3.5rem);
	}
	.sidebar {
		background-color: #333;
		color: white;
		padding-left: 0.5rem;
		padding-top: 0.5rem;
		display: flex;
		flex-direction: column;
	}
	.message-box {
		margin-left: 1rem;
	}
</style>
