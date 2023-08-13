<script>
  import Blogs from "./blogs.svelte";
  import "./global.css";
</script>

<main class="relative top-16">
  <Blogs />
</main>

<!-- <script>
  let enableFoo;
</script>

<button on:click={() => (enableFoo = !enableFoo)}> Toggle Class </button>
<p class={enableFoo ? "foo" : "unfoo"}>foo</p>

<style>
  .foo {
    color: red;
  }
  .unfoo {
    color: blue;
  }
</style> -->
