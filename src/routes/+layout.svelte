<nav>
	<a href="/">home</a>
	<a href="/login">login</a>
	<a href="/signup">signup</a>
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
</style>
