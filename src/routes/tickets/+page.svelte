<script>
  import { onMount } from 'svelte';
  import { createClient } from '@supabase/supabase-js';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let tickets = [];

  onMount(async () => {
    const { data, error } = await supabase
      .from('tickets')
      .select('*');

    if (error) {
      console.error('Error fetching tickets:', error);
    } else {
      tickets = data;
    }
  });
</script>

<h1>Tickets</h1>

<a href="/tickets/new" class="button">Create New Ticket</a>

{#if tickets.length > 0}
  <ul>
    {#each tickets as ticket}
      <li>
        <a href="/tickets/{ticket.id}">{ticket.jira_id} - {ticket.title}</a>
      </li>
    {/each}
  </ul>
{:else}
  <p>No tickets found.</p>
{/if}

<style>
  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin-bottom: 0.5rem;
  }

  a {
    text-decoration: none;
    color: #0066cc;
  }

  a:hover {
    text-decoration: underline;
  }

  .button {
    display: inline-block;
    background-color: #4CAF50;
    color: white;
    padding: 10px 20px;
    text-align: center;
    text-decoration: none;
    border-radius: 4px;
    margin-bottom: 1rem;
  }
</style>