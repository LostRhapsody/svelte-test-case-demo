<script>
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  import { createClient } from '@supabase/supabase-js';
  import marked from 'marked';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let ticket = null;
  let testCases = [];
  let testResults = {};

  onMount(async () => {
    const ticketId = $page.params.id;

    // Fetch ticket details
    const { data: ticketData, error: ticketError } = await supabase
      .from('tickets')
      .select('*')
      .eq('id', ticketId)
      .single();

    if (ticketError) {
      console.error('Error fetching ticket:', ticketError);
    } else {
      ticket = ticketData;
    }

    // Fetch test cases for the ticket
    const { data: testCaseData, error: testCaseError } = await supabase
      .from('test_cases')
      .select('*')
      .in('module', ticket.modules)
      .eq('customer_specific', ticket.customer_specific);

    if (testCaseError) {
      console.error('Error fetching test cases:', testCaseError);
    } else {
      testCases = testCaseData;
    }

    // Fetch existing test results
    const { data: testResultData, error: testResultError } = await supabase
      .from('test_results')
      .select('*')
      .eq('ticket_id', ticketId);

    if (testResultError) {
      console.error('Error fetching test results:', testResultError);
    } else {
      testResultData.forEach(result => {
        testResults[result.test_case_id] = result.result;
      });
    }
  });

  async function saveTestResult(testCaseId, result) {
    const { error } = await supabase
      .from('test_results')
      .upsert({
        ticket_id: ticket.id,
        test_case_id: testCaseId,
        result: result
      });

    if (error) {
      console.error('Error saving test result:', error);
    } else {
      testResults[testCaseId] = result;
    }
  }
</script>

<h1>{ticket ? ticket.title : 'Loading...'}</h1>
<p>Jira ID: {ticket ? ticket.jira_id : ''}</p>

{#if testCases.length > 0}
  <h2>Test Cases</h2>
  {#each testCases as testCase}
    <div class="test-case">
      <h3>{testCase.title}</h3>
      <div class="content">
        {@html marked(testCase.content)}
      </div>
      <div class="result">
        <label>
          <input 
            type="radio" 
            name={`result-${testCase.id}`} 
            value="pass" 
            checked={testResults[testCase.id] === 'pass'}
            on:change={() => saveTestResult(testCase.id, 'pass')}
          >
          Pass
        </label>
        <label>
          <input 
            type="radio" 
            name={`result-${testCase.id}`} 
            value="fail" 
            checked={testResults[testCase.id] === 'fail'}
            on:change={() => saveTestResult(testCase.id, 'fail')}
          >
          Fail
        </label>
      </div>
    </div>
  {/each}
{:else}
  <p>No test cases found for this ticket.</p>
{/if}

<style>
  .test-case {
    border: 1px solid #ccc;
    padding: 1rem;
    margin-bottom: 1rem;
  }

  .content {
    margin-bottom: 1rem;
  }

  .result {
    display: flex;
    gap: 1rem;
  }
</style>