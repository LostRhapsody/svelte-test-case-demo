<script>
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  import { createClient } from '@supabase/supabase-js';
  import { goto } from '$app/navigation';

  const supabase = createClient('https://cffihqmnuodtupbkeeql.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImNmZmlocW1udW9kdHVwYmtlZXFsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mjc5NzYzMTUsImV4cCI6MjA0MzU1MjMxNX0.D1rMhyTzCbA_ApWsr4Q6aPRj8YhyG2MSMaJhQ9xpgbs');

  let user = null;

  onMount(async () => {
    const { data: { user: currentUser } } = await supabase.auth.getUser();
    user = currentUser;

    supabase.auth.onAuthStateChange((event, session) => {
      user = session?.user || null;
    });
  });

  async function signOut() {
    await supabase.auth.signOut();
    goto('/login');
  }
</script>

<div class="app">
  <header>
    {#if user}
      <span>Welcome, {user.email}</span>
      <button on:click={signOut}>Sign Out</button>
    {:else}
      <a href="/login">Login</a>
      <a href="/signup">Sign Up</a>
    {/if}
  </header>

  {#if user}
    <aside>
      <nav>
        <ul>
          <li class:active={$page.url.pathname === '/'}>
            <a href="/">Home</a>
          </li>
          <li class:active={$page.url.pathname.startsWith('/projects')}>
            <a href="/projects">Projects</a>
          </li>
          <li class:active={$page.url.pathname.startsWith('/test-cases')}>
            <a href="/test-cases">Test Cases</a>
          </li>
          <li class:active={$page.url.pathname.startsWith('/tickets')}>
            <a href="/tickets">Tickets</a>
          </li>
        </ul>
      </nav>
    </aside>
  {/if}

  <main>
    <slot />
  </main>
</div>

<style>
  .app {
    display: flex;
    flex-direction: column;
    height: 100vh;
  }

  header {
    background-color: #f0f0f0;
    padding: 1rem;
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }

  header a, header button {
    margin-left: 1rem;
  }

  aside {
    width: 200px;
    background-color: #f0f0f0;
    padding: 1rem;
  }

  main {
    flex-grow: 1;
    padding: 1rem;
    overflow-y: auto;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin-bottom: 0.5rem;
  }

  .active {
    font-weight: bold;
  }

  a {
    text-decoration: none;
    color: #333;
  }

  a:hover {
    text-decoration: underline;
  }
</style>