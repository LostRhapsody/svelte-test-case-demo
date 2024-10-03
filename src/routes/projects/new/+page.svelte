<script>
  import { createClient } from '@supabase/supabase-js';
  import { goto } from '$app/navigation';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let name = '';
  let errorMessage = '';

  async function createProject() {
    const { data: { user } } = await supabase.auth.getUser();
    
    if (!user) {
      errorMessage = 'You must be logged in to create a project.';
      return;
    }

    const { data, error } = await supabase
      .from('projects')
      .insert({ name, user_id: user.id })
      .select();

    if (error) {
      console.error('Error creating project:', error);
      errorMessage = error.message;
    } else {
      goto('/projects');
    }
  }
</script>

<h1>Create New Project</h1>

<form on:submit|preventDefault={createProject}>
  <div>
    <label for="name">Project Name:</label>
    <input id="name" bind:value={name} required>
  </div>

  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}

  <button type="submit">Create Project</button>
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

  input {
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

  .error {
    color: red;
  }
</style>