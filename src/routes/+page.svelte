<script lang="ts">
	import { onMount } from "svelte";
	import { z } from "zod";
	import ChatInput from "./chatinput.svelte";

	const ChatroomSchema = z.object({
		id: z.number(),
		name: z.string()
	});

	type Chatroom = z.infer<typeof ChatroomSchema>;

	const MessageSchema = z.object({
		id: z.number(),
		time: z.string(),
		body: z.string(),
		user_id: z.number(),
		chatroom_id: z.number()
	});

	type Message = z.infer<typeof MessageSchema>;

	let chatrooms: Chatroom[] = [];
	let messages: Message[] = [];

	// this value is modified in mount
	let room_selected = 0;

	function fetchMessages(room_selected: number) {
		fetch(`http://localhost:4000/api/messages/${room_selected}`)
			.then((res) => res.json())
			.then((data) => (messages = z.array(MessageSchema).parse(data ?? [])))
			.catch((err) => console.error(err));
	}

	$: {
		fetchMessages(room_selected);
	}

	onMount(() => {
		fetch("http://localhost:4000/api/chatrooms")
			.then((res) => res.json())
			.then((data) => {
        chatrooms = z.array(ChatroomSchema).parse(data);
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
          <h3>{message.user?.name ?? "user " + message.user_id}</h3>
					<p>{message.body}</p>
				</div>
			{/each}
		</div>
		<ChatInput />
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
