<!-- <script>
  import { onMount } from 'svelte';
  import CourseList from './components/CourseList.svelte';
  import Navbar from './components/Navbar.svelte';
  import { jwt } from './stores/auth';
  
  let token = '';
  
  // Login function (simplified for demo)
  async function login() {
    const response = await fetch('http://localhost:5000/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ username: 'admin', password: 'password' })
    });
    const data = await response.json();
    token = data.access_token;
    jwt.set(token);
  }
  
  onMount(login);
</script>

<svelte:head>
  <title>Course Management System</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <link rel="stylesheet" href="./styles/global.css">
</svelte:head>

<Navbar />

<div class="container mt-4">
  {#if token}
    <CourseList />
  {:else}
    <div class="text-center">
      <p>Loading...</p>
    </div>
  {/if}
</div> -->

<script>
  // Import Bootstrap CSS (add these lines)
  import 'bootstrap/dist/css/bootstrap.min.css';
  import 'bootstrap-icons/font/bootstrap-icons.css';
  import './styles/global.css';
  
  import { onMount } from 'svelte';
  import CourseList from './components/CourseList.svelte';
  import Navbar from './components/Navbar.svelte';
  import { jwt } from './stores/auth';
  
  let token = '';
  
  // Login function (simplified for demo)
  async function login() {
    const response = await fetch('http://localhost:5000/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ username: 'admin', password: 'password' })
    });
    const data = await response.json();
    token = data.access_token;
    jwt.set(token);
  }
  
  onMount(login);
</script>

<svelte:head>
  <title>Course Management System</title>
</svelte:head>

<Navbar />

<div class="container mt-4">
  {#if token}
    <CourseList />
  {:else}
    <div class="text-center">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
      <p class="mt-2">Authenticating...</p>
    </div>
  {/if}
</div>