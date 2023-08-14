<script lang="ts">
  export let users = [];

  import { Button, Modal, Label, Input } from 'flowbite-svelte';
  let formModal = false;
  let userName = "";

  function submitForm() {
   // console.log(userName);
    const query = `mutation ($username: String!) {
      createUser(username: $username) {
        id
        username
      }
    }`;

    const variables = { "username": userName };

    fetch('https://rnd-ns2-tech-challenge-next-be.vercel.app/api/graphql', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ query, variables }),
    }).then(response => response.json())
    // .then(respobj => {
    // console.log(respobj);
    // })
    .catch(error => console.error(error));

   formModal = false;
  }
</script>

<table>
  <thead>
    <tr>
      <th>ID</th>
      <th>Username</th>
      <th>Companies</th>
    </tr>
  </thead>
  <tbody>
    {#each users as user}
      <tr>
        <td>{user.id}</td>
        <td>{user.username}</td>
        <td>{user.companies.map(c => c.name ).join(", ")}</td>
      </tr>
    {/each}
  </tbody>
</table>

<br/>

<Button on:click={() => (formModal = true)}>Add new user</Button>
<Modal bind:open={formModal} size="xs" autoclose={false} class="w-full">
  <form class="flex flex-col space-y-6" on:submit|preventDefault={submitForm}>

    <h3 class="mb-4 text-xl font-medium text-gray-900 dark:text-white">Add a new user</h3>

    <Label class="space-y-2">
      <Input type="text" name="username" placeholder="username" required bind:value="{userName}" />
    </Label>
   
    <Button type="submit" class="w-full1">Save</Button>
  
  </form>
</Modal>
