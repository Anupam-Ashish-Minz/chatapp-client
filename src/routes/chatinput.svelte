<script lang="ts">
  let message = "";

  function sendMessage() {
    // TODO change this to websocket later on
    fetch("http://localhost:4000/api/message")
      .then(res => res.json())
      .then(data => console.log(data))
      .catch(err => console.error(err));
  }
</script>

<form on:submit|preventDefault={sendMessage}>
  <input type="text" bind:value={message}>
  <button>send</button>
</form>

<style>
  form {
    display: flex;
    flex-direction: row;
    gap: 1rem;
    position: absolute;
    bottom: 1rem;
  }

  form > input {
    border-radius: 5px;
  }

  form > button {
    border-radius: 5px;
    background-color: #1C30FF;
    border: none;
    color: white;
    padding: 0.5rem 1rem 0.5rem 1rem;
  }
</style>
