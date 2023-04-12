<script>
  import Login from "./lib/Login.svelte";
  import Posts from "./lib/Posts.svelte";

</script>

<nav>
  <h1>PB Test</h1>
  <Login />
</nav>

<main>
  <Posts />
</main>

<style>
  nav {
    position: sticky;
    top: 0;
    display: flex;
    align-items: center;
    gap: 20px;
    background-color: white;
    color: black;
    padding: 20px;
    justify-content: space-between;
    border-bottom: 1px solid rgb(230, 230, 230);
  }

  nav h1 {
    color: #66a5aa;
  }

  main {
    flex-grow: 3;
    background-color: white;
    display: flex;
  }
</style>