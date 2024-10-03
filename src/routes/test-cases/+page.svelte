<script>
  import { onMount } from 'svelte';
  import { createClient } from '@supabase/supabase-js';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let testCases = [];
  let loading = true;
  let error = null;

  onMount(async () => {
    try {
      const { data, error: fetchError } = await supabase
        .from('test_cases')
        .select('*');

      if (fetchError) {
        throw fetchError;
      }

      testCases = data;
    } catch (e) {
      console.error('Error fetching test cases:', e);
      error = 'Failed to load test cases. Please try again later.';
    } finally {
      loading = false;
    }
  });
</script>

<svelte:head>
  <title>Test Cases</title>
</svelte:head>

<h1>Test Cases</h1>

<a href="/test-cases/new" class="button">Create New Test Case</a>

{#if loading}
  <p>Loading test cases...</p>
{:else if error}
  <p class="error">{error}</p>
{:else if testCases.length > 0}
  <ul>
    {#each testCases as testCase}
      <li>
        <a href="/test-cases/{testCase.id}">{testCase.title}</a>
      </li>
    {/each}
  </ul>
{:else}
  <p>No test cases found.</p>
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

  .error {
    color: red;
  }
</style>