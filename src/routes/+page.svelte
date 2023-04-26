<script lang="ts">
	import { onMount } from "svelte";
	import { z } from "zod";

	enum REQUEST_STATUS {
		PENDING,
		ERROR,
		COMPLETED
	}

	const ChatroomSchema = z.object({
		id: z.number(),
		name: z.string()
	});

	type Chatroom = z.infer<typeof ChatroomSchema>;

	const MessageSchema = z.object({
		id: z.number(),
		time: z.string(),
		body: z.string(),
		user: z.optional(
			z.object({
				id: z.number(),
				name: z.string(),
				email: z.string()
			})
		),
		user_id: z.number(),
		chatroom_id: z.number()
	});

	type Message = z.infer<typeof MessageSchema>;

	let chatrooms: Chatroom[] = [];
	let messages: Message[] = [];
	let messageStatus = REQUEST_STATUS.PENDING;

	// this value is modified in mount
	let room_selected = 0;

	function fetchMessages(room_selected: number) {
		fetch(`http://localhost:4000/api/messages/${room_selected}`, {
			method: "get",
			credentials: "include"
		})
			.then((res) => {
				if (res.status === 401) {
					alert("login required");
					// window.location.href = "/login";
				}
				return res.json();
			})
			.then((data) => {
				messages = z.array(MessageSchema).parse(data ?? []);
				messageStatus = REQUEST_STATUS.COMPLETED;
			})
			.catch((err) => {
				console.error(err);
				messageStatus = REQUEST_STATUS.ERROR;
			});
	}

	$: {
		if (chatrooms.filter((x) => x.id === room_selected).length > 0) {
			fetchMessages(room_selected);
		}
	}

	let message = "";
	let socket: WebSocket | undefined;

	function sendMessage() {
		socket?.send(
			JSON.stringify({
				body: message,
				chatroom_id: room_selected
			})
		);
		message = "";
	}

	onMount(() => {
		fetch("http://localhost:4000/api/chatrooms")
			.then((res) => res.json())
			.then((data) => {
				chatrooms = z.array(ChatroomSchema).parse(data ?? []);
				room_selected = chatrooms[0].id;
			});

		socket = new WebSocket("ws://localhost:4000/ws/message");

		socket.onmessage = (e) => {
			messages.push(MessageSchema.parse(JSON.parse(e.data)));
			messages = messages;
		};
	});
</script>

<main>
	<div class="sidebar">
		{#each chatrooms as room}
			{#if room.id === room_selected}
				<button class="room-selector room-selected">{room.name}</button>
			{:else}
				<button
					class="room-selector"
					on:click={() => {
						room_selected = room.id;
					}}
				>
					{room.name}
				</button>
			{/if}
		{/each}
	</div>
	{#if messageStatus === REQUEST_STATUS.COMPLETED}
		<div class="message-box">
			{#if messages.length > 0}
				<div class="message-list">
          <h2>list of messages</h2>
					{#each messages as message}
						<div>
							<h3>{message.user?.name ?? "user " + message.user_id}</h3>
							<p>{message.body}</p>
						</div>
					{/each}
				</div>
			{:else}
				<h2>Enter the first message</h2>
			{/if}
			<form on:submit|preventDefault={sendMessage} class="chatbox">
				<input type="text" bind:value={message} />
				<button>send</button>
			</form>
		</div>
	{:else if messageStatus === REQUEST_STATUS.PENDING}
		<div>loading...</div>
	{:else if messageStatus === REQUEST_STATUS.ERROR}
		<div>something went wrong</div>
	{:else}
		<div>server responded with error</div>
	{/if}
</main>

<style>
	main {
		display: grid;
		grid-template-columns: 1fr 5fr;
		height: calc(100vh - 3.5rem);
	}

	.sidebar {
		background-color: #222;
		color: white;
		padding-left: 0.5rem;
		padding-top: 0.5rem;
		display: flex;
		flex-direction: column;
	}

	.message-box {
		padding-left: 1rem;
    position: relative;
	}

	.message-list {
    --offset: 10rem;
		--mask: linear-gradient(
				to bottom,
				rgba(0, 0, 0, 1) 0,
				rgba(0, 0, 0, 1) 80%,
				rgba(0, 0, 0, 0) 95%,
				rgba(0, 0, 0, 0) 0
			)
			100% 50% / 100% 100% repeat-x;

		overflow: auto;
		height: calc(90vh - var(--offset));
    padding-bottom: var(--offset);

		-webkit-mask: var(--mask);
		mask: var(--mask);
	}

	.chatbox {
		display: flex;
		flex-direction: row;
		gap: 1rem;
		position: absolute;
		bottom: 1rem;
	}

	.chatbox > input {
		border-radius: 5px;
		width: 70vw;
		border: 1px solid rgba(0, 0, 0, 0.5);
		padding: 0.5rem;
	}

	.chatbox > button {
		border-radius: 5px;
		background-color: #1c30ff;
		border: none;
		color: white;
		/* padding: 0.5rem 1rem 0.5rem 1rem; */
		padding-left: 2rem;
		padding-right: 2rem;
	}

	.room-selector {
		background-color: transparent;
		text-align: start;
		color: inherit;
		border: none;
		padding: 1rem 2rem 1rem 2rem;
		margin-right: 0.5rem;
		border-radius: 5px;
		cursor: pointer;
	}

	.room-selected {
		background-color: #444;
	}
</style>
