<script>
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  import { createClient } from '@supabase/supabase-js';
  import marked from 'marked';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let testCase = null;

  onMount(async () => {
    const testCaseId = $page.params.id;

    const { data, error } = await supabase
      .from('test_cases')
      .select('*')
      .eq('id', testCaseId)
      .single();

    if (error) {
      console.error('Error fetching test case:', error);
    } else {
      testCase = data;
    }
  });
</script>

{#if testCase}
  <h1>{testCase.title}</h1>
  <p>Module: {testCase.module}</p>
  <p>Type: {testCase.customer_specific ? 'Customer-Specific' : 'Standard'}</p>
  <div class="tags">
    {#each testCase.tags as tag}
      <span class="tag">{tag}</span>
    {/each}
  </div>
  <div class="content">
    {@html marked(testCase.content)}
  </div>
{:else}
  <p>Loading test case...</p>
{/if}

<style>
  .tags {
    margin-bottom: 1rem;
  }

  .tag {
    background-color: #e0e0e0;
    padding: 0.2rem 0.5rem;
    border-radius: 4px;
    margin-right: 0.5rem;
  }

  .content {
    line-height: 1.6;
  }

  .content :global(img) {
    max-width: 100%;
    height: auto;
  }

  .content :global(pre) {
    background-color: #f4f4f4;
    padding: 1rem;
    border-radius: 4px;
    overflow-x: auto;
  }
</style>