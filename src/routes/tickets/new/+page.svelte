<script>
  import { createClient } from '@supabase/supabase-js';
  import { goto } from '$app/navigation';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let jiraId = '';
  let title = '';
  let selectedModules = [];
  let isCustomerSpecific = false;

  let modules = ['order entry', 'cart', 'checkout']; // Add more modules as needed

  async function createTicket() {
    const { data, error } = await supabase
      .from('tickets')
      .insert({
        jira_id: jiraId,
        title: title,
        modules: selectedModules,
        customer_specific: isCustomerSpecific
      })
      .select();

    if (error) {
      console.error('Error creating ticket:', error);
    } else {
      goto(`/tickets/${data[0].id}`);
    }
  }
</script>

<h1>Create New Ticket</h1>

<form on:submit|preventDefault={createTicket}>
  <div>
    <label for="jira-id">Jira ID:</label>
    <input id="jira-id" bind:value={jiraId} required>
  </div>

  <div>
    <label for="title">Title:</label>
    <input id="title" bind:value={title} required>
  </div>

  <div>
    <label>Modules:</label>
    {#each modules as module}
      <label>
        <input type="checkbox" bind:group={selectedModules} value={module}>
        {module}
      </label>
    {/each}
  </div>

  <div>
    <label>
      <input type="checkbox" bind:checked={isCustomerSpecific}>
      Customer-Specific
    </label>
  </div>

  <button type="submit">Create Ticket</button>
</form>

<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  label {
    font-weight: bold;
  }

  input[type="text"] {
    width: 100%;
    padding: 0.5rem;
  }

  button {
    background-color: #4CAF50;
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 2px;
    cursor: pointer;
  }
</style>