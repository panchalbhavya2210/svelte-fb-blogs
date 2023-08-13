<script>
  import Blogcreator from "./blogcreator.svelte";
  import "./global.css";
</script>

<main class="relative top-16 -z-10">
  <Blogcreator />
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
