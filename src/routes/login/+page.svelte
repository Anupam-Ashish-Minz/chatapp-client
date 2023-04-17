<script lang="ts">
	let email = "";
	let password = "";
	function login() {
		fetch("http://localhost:4000/login", {
			method: "post",
			body: JSON.stringify({
				email: email,
				password: password
			}),
			credentials: "include",
			headers: {
				"Content-Type": "application/json"
			}
		}).then((res) => {
			if (res.status === 200) window.location.href = "/";
		});
	}
</script>

<form on:submit|preventDefault={login}>
	<div>
		<label for="email">email</label>
		<input type="email" bind:value={email} />
	</div>
	<div>
		<label for="password">password</label>
		<input type="password" bind:value={password} />
	</div>
	<a href="/signup">don't have a password sigunp</a>
	<button> login </button>
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
		border-radius: 0.2rem;
		border: 1px solid rgba(0, 0, 0, 0.5);
	}

	form > button {
		padding: 0.5rem 1rem 0.5rem 1rem;
		background-color: #4d40ef;
		color: white;
		border: none;
		border-radius: 0.2rem;
	}
</style>
