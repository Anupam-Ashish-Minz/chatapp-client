<script lang="ts">
	let name = "";
	let email = "";
	let password = "";

	function signup() {
		fetch("http://localhost:4000/api/auth/signup", {
			method: "post",
			body: JSON.stringify({
				name: name,
				email: email,
				password: password
			}),
			credentials: "include",
			headers: {
				"Content-Type": "application/json"
			}
		});
	}
</script>

<form on:submit|preventDefault={signup}>
	<div>
		<label for="name">name</label>
		<input type="text" bind:value={name} />
	</div>
	<div>
		<label for="email">email</label>
		<input type="email" bind:value={email} />
	</div>
	<div>
		<label for="password">password</label>
		<input type="password" bind:value={password} />
	</div>
	<a href="/signup">already have a password login</a>
	<button> signup </button>
</form>

<style>
	form {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		align-items: flex-start;
		padding: 2rem;
	}

	form * {
		text-transform: capitalize;
	}

	form > div {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}

	form > div > input {
		padding: 0.25rem 1rem 0.25rem 1rem;
		text-transform: none;
	}

	form > button {
		padding: 0.5rem 1rem 0.5rem 1rem;
		background-color: #4d40ef;
		color: white;
		outline: none;
		border: none;
		border-radius: 0.2rem;
	}
</style>
