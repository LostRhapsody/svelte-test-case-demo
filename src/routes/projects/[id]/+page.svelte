<script>
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  import { createClient } from '@supabase/supabase-js';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let project = null;
  let testCases = [];

  onMount(async () => {
    const projectId = $page.params.id;

    // Fetch project details
    const { data: projectData, error: projectError } = await supabase
      .from('projects')
      .select('*')
      .eq('id', projectId)
      .single();

    if (projectError) {
      console.error('Error fetching project:', projectError);
    } else {
      project = projectData;
    }

    // Fetch test cases for the project
    const { data: testCaseData, error: testCaseError } = await supabase
      .from('test_cases')
      .select('*')
      .eq('project_id', projectId);

    if (testCaseError) {
      console.error('Error fetching test cases:', testCaseError);
    } else {
      testCases = testCaseData;
    }
  });

  function groupTestCases(cases) {
    const grouped = {
      Standard: {},
      'Customer-Specific': {}
    };

    cases.forEach(testCase => {
      const group = testCase.customer_specific ? 'Customer-Specific' : 'Standard';
      if (!grouped[group][testCase.module]) {
        grouped[group][testCase.module] = [];
      }
      grouped[group][testCase.module].push(testCase);
    });

    return grouped;
  }

  $: groupedTestCases = groupTestCases(testCases);
</script>

<h1>{project ? project.name : 'Loading...'}</h1>

{#if project}
  {#each Object.entries(groupedTestCases) as [group, modules]}
    <h2>{group}</h2>
    {#each Object.entries(modules) as [module, cases]}
      <h3>{module}</h3>
      <ul>
        {#each cases as testCase}
          <li>
            <a href="/test-cases/{testCase.id}">{testCase.title}</a>
          </li>
        {/each}
      </ul>
    {/each}
  {/each}
{:else}
  <p>Loading test cases...</p>
{/if}

<style>
  h2 {
    color: #444;
  }

  h3 {
    color: #666;
  }

  ul {
    list-style-type: none;
    padding-left: 20px;
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
</style>