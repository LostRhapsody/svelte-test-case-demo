<script>
  import { goto } from '$app/navigation';

  function navigateToProjects() {
    goto('/projects');
  }
</script>

<h1>ERP/WMS Test Case Management</h1>
<p>Welcome to the ERP/WMS Test Case Management application. Here you can manage and perform test cases for your ERP/WMS system.</p>
<button on:click={navigateToProjects}>View Projects</button>

<style>
  h1 {
    color: #333;
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