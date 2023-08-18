<script>
  import { page } from "$app/stores";
  import { initializeApp } from "firebase/app";
  import { getAuth, signOut } from "firebase/auth";

  import { onMount } from "svelte";
  import { getDatabase, ref, get } from "firebase/database";

  const firebaseConfig = {
    apiKey: import.meta.env.VITE_API_KEY,
    authDomain: import.meta.env.VITE_AUTH_DOMAIN,
    projectId: import.meta.env.VITE_PRJ_ID,
    storageBucket: import.meta.env.VITE_STG_BKT,
    messagingSenderId: import.meta.env.VITE_MSI,
    appId: import.meta.env.VITE_APID,
    measurementId: import.meta.env.VITE_MID,
  };
  // if (getApps().length == 0) {
  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);
  const getDb = getDatabase(app);

  let profileImage;
  function readDb() {
    let userId = auth.currentUser.uid;
    get(ref(getDb, userId)).then((data) => {
      profileImage = data.val().userUrl;
    });
  }
  onMount(() => {
    setTimeout(() => {
      readDb();
    }, 1900);
  });
  let state;
  let stateNav;
  function stateChanger() {
    state = !state;
  }
  function stateChangerNav() {
    stateNav = !stateNav;
  }
  let successState;
  function signOutUser() {
    signOut(auth).then(() => {
      successState = !successState;

      setTimeout(() => {
        successState = !successState;
      }, 3000);
    });
  }
</script>

<nav class="bg-slate-200 fixed w-screen z-50">
  <div class="mx-auto max-w-7xl px-2 sm:px-6 lg:px-8">
    <div class="relative flex h-16 items-center justify-between">
      <div class="absolute inset-y-0 left-0 flex items-center sm:hidden">
        <!-- Mobile menu button-->
        <button
          type="button"
          class="relative inline-flex items-center justify-center rounded-md p-2 text-gray-400 hover:bg-gray-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white"
          aria-controls="mobile-menu"
          aria-expanded="false"
          on:click={stateChangerNav}
        >
          <span class="absolute -inset-0.5" />
          <span class="sr-only">Open main menu</span>
          <!--
              Icon when menu is closed.
  
              Menu open: "hidden", Menu closed: "block"
            -->

          <svg
            class="h-6 w-6 {stateNav ? 'hidden' : 'block'}"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            aria-hidden="true"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5"
            />
          </svg>

          <svg
            class="h-6 w-6 {stateNav ? 'block' : 'hidden'}"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            aria-hidden="true"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        </button>
      </div>
      <div
        class="flex flex-1 items-center justify-center sm:items-stretch sm:justify-start"
      >
        <div class="flex flex-shrink-0 items-center">
          <a href="/home" target="_blank">
            <img
              class="h-8 w-auto"
              src="https://raw.githubusercontent.com/panchalbhavya2210/svelte-fb-blogs/ae19ecb2d4241abb88ca729b3f600665b04ea392/static/logo.svg"
              alt="Your Company"
            /></a
          >
        </div>
        <div class="hidden sm:ml-6 sm:block">
          <div class="flex space-x-4">
            <a
              href="/"
              class=" text-black hover:bg-indigo-200 rounded-md px-3 py-2 text-sm font-medium aria"
              aria-current={$page.url.pathname === "/" ? "page" : undefined}
              >Home</a
            >
            <a
              href="/Auth"
              aria-current={$page.url.pathname === "/Auth" ? "page" : undefined}
              class="text-black hover:bg-indigo-200 rounded-md px-3 py-2 text-sm font-medium aria"
              >Sign Up</a
            >
            <a
              href="/Login"
              class="text-black hover:bg-indigo-200 rounded-md px-3 py-2 text-sm font-medium aria"
              aria-current={$page.url.pathname === "/Login"
                ? "page"
                : undefined}>Log In</a
            >
            <a
              href="/Creator"
              class="text-black hover:bg-indigo-200 rounded-md px-3 py-2 text-sm font-medium aria"
              aria-current={$page.url.pathname === "/Creator"
                ? "page"
                : undefined}>Create A Blog</a
            >
          </div>
        </div>
      </div>
      <div
        class="absolute inset-y-0 right-0 flex items-center pr-2 sm:static sm:inset-auto sm:ml-6 sm:pr-0"
      >
        <!-- Profile dropdown -->
        <div class="relative ml-3">
          <div>
            <button
              type="button"
              on:click={stateChanger}
              class="relative flex rounded-full bg-gray-800 text-sm focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-gray-800"
              id="user-menu-button"
              aria-expanded="false"
              aria-haspopup="true"
            >
              <span class="absolute -inset-1.5" />
              <span class="sr-only">Open user menu</span>
              <img class="h-12 w-12 rounded-full" src={profileImage} alt="" />
            </button>
          </div>

          <div
            class="{state
              ? 'scale-100'
              : 'scale-0'} transition-all absolute right-0 z-10 mt-2 w-48 origin-top-right rounded-md bg-white py-1 shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
            role="menu"
            aria-orientation="vertical"
            aria-labelledby="user-menu-button"
            tabindex="-1"
          >
            <a href="/MyProfile" rel="noopener noreferrer">
              <button
                class="block px-4 py-2 w-48 text-sm text-gray-700 aria hover:bg-indigo-200"
                id="user-menu-item-2"
                on:click={stateChanger}
              >
                My Profile
              </button>
            </a>
            <button
              class="block px-4 py-2 w-48 text-sm text-gray-700 aria hover:bg-indigo-200"
              id="user-menu-item-2"
              on:click={signOutUser}>Sign out</button
            >
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Mobile menu, show/hide based on menu state. -->
  <div
    class="sm:hidden {stateNav
      ? 'translate-y-0 h-100'
      : '-translate-y-96 h-0'} transition-all overflow-hidden"
    id="mobile-menu"
  >
    <div class="space-y-1 px-2 pb-3 pt-2">
      <!-- Current: "bg-gray-900 text-white", Default: "text-gray-300 hover:bg-gray-700 hover:text-white" -->
      <a
        href="/"
        class="  block rounded-md px-3 py-2 text-base font-medium aria"
        aria-current={$page.url.pathname === "/" ? "page" : undefined}
        on:click={() => (stateNav = !stateNav)}>Home</a
      >
      <a
        href="/Auth"
        aria-current={$page.url.pathname === "/Auth" ? "page" : undefined}
        class="text-black hover:bg-indigo-200 block rounded-md px-3 py-2 text-base font-medium aria"
        on:click={() => (stateNav = !stateNav)}>Sign Up</a
      >
      <a
        href="/Login"
        class="text-black hover:bg-indigo-200 block rounded-md px-3 py-2 text-base font-medium aria"
        aria-current={$page.url.pathname === "/Login" ? "page" : undefined}
        on:click={() => (stateNav = !stateNav)}>Login</a
      >
      <a
        href="/Creator"
        class="text-black hover:bg-indigo-200 block rounded-md px-3 py-2 text-base font-medium aria"
        aria-current={$page.url.pathname === "/Creator" ? "page" : undefined}
        on:click={() => (stateNav = !stateNav)}>Create Blog</a
      >
    </div>
  </div>
  <div
    class="p-4 mb-4 text-sm text-green-800 rounded-lg bg-green-50 dark:bg-green-100 shadow-lg fixed bottom-0 dark:text-green-900 transition {successState
      ? 'translate-y-0 opacity-100'
      : 'translate-y-36 opacity-0'}"
    role="alert"
  >
    <span class="font-medium">Success!</span> Logged Out.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  </div>
</nav>

<style>
  .aria[aria-current="page"] {
    color: #fff;
    background-color: #4f46e5;
    transition: 0.5s;
    border-radius: 100px;
  }
</style>
