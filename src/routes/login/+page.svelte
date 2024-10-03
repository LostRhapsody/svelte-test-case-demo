<script>
  import { createClient } from '@supabase/supabase-js';
  import { goto } from '$app/navigation';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let email = '';
  let password = '';
  let errorMessage = '';

  async function handleLogin() {
    const { error } = await supabase.auth.signInWithPassword({ email, password });
    if (error) {
      errorMessage = error.message;
    } else {
      goto('/');
    }
  }
</script>

<h1>Login</h1>

<form on:submit|preventDefault={handleLogin}>
  <div>
    <label for="email">Email:</label>
    <input id="email" type="email" bind:value={email} required>
  </div>

  <div>
    <label for="password">Password:</label>
    <input id="password" type="password" bind:value={password} required>
  </div>

  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}

  <button type="submit">Login</button>
</form>

<p>Don't have an account? <a href="/signup">Sign up</a></p>

<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    max-width: 300px;
    margin: 0 auto;
  }

  .error {
    color: red;
  }
</style>