<script>
  import { createClient } from '@supabase/supabase-js';
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let title = '';
  let content = '';
  let module = '';
  let customerSpecific = false;
  let tags = '';
  let projectId = '';
  let projects = [];

  onMount(async () => {
    const { data, error } = await supabase
      .from('projects')
      .select('id, name');

    if (error) {
      console.error('Error fetching projects:', error);
    } else {
      projects = data;
    }
  });

  async function createTestCase() {
    const { data, error } = await supabase
      .from('test_cases')
      .insert({
        title,
        content,
        module,
        customer_specific: customerSpecific,
        tags: tags.split(',').map(tag => tag.trim()),
        project_id: projectId
      })
      .select();

    if (error) {
      console.error('Error creating test case:', error);
    } else {
      goto('/test-cases');
    }
  }
</script>

<h1>Create New Test Case</h1>

<form on:submit|preventDefault={createTestCase}>
  <div>
    <label for="title">Title:</label>
    <input id="title" bind:value={title} required>
  </div>

  <div>
    <label for="content">Content:</label>
    <textarea id="content" bind:value={content} required></textarea>
  </div>

  <div>
    <label for="module">Module:</label>
    <input id="module" bind:value={module} required>
  </div>

  <div>
    <label>
      <input type="checkbox" bind:checked={customerSpecific}>
      Customer-Specific
    </label>
  </div>

  <div>
    <label for="tags">Tags (comma-separated):</label>
    <input id="tags" bind:value={tags}>
  </div>

  <div>
    <label for="project">Project:</label>
    <select id="project" bind:value={projectId} required>
      <option value="">Select a project</option>
      {#each projects as project}
        <option value={project.id}>{project.name}</option>
      {/each}
    </select>
  </div>

  <button type="submit">Create Test Case</button>
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

  input, textarea, select {
    width: 100%;
    padding: 0.5rem;
  }

  textarea {
    height: 150px;
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