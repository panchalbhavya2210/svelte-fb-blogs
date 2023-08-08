<script>
  import { initializeApp, getApps } from "firebase/app";
  import { getAuth, sendPasswordResetEmail } from "firebase/auth";
  import "../global.css";

  let successState;
  let alertState;

  const firebaseConfig = {
    apiKey: import.meta.env.VITE_API_KEY,
    authDomain: import.meta.env.VITE_AUTH_DOMAIN,
    projectId: import.meta.env.VITE_PRJ_ID,
    storageBucket: import.meta.env.VITE_STG_BKT,
    messagingSenderId: import.meta.env.VITE_MSI,
    appId: import.meta.env.VITE_APID,
    measurementId: import.meta.env.VITE_MID,
  };
  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);
  function sendLink() {
    let email = "a@gmail.com";
    sendPasswordResetEmail(auth, email)
      .then(() => {
        successState = !successState;
        setTimeout(() => {
          successState = !successState;
        }, 3000);
      })
      .catch((err) => {
        console.log(err);
        alertState = !alertState;
        setTimeout(() => {
          alertState = !alertState;
        }, 3000);
      });
  }
</script>

<main>
  <div class="flex min-h-full flex-col justify-center px-6 py-12 lg:px-8">
    <div class="sm:mx-auto sm:w-full sm:max-w-sm">
      <img
        class="mx-auto h-10 w-auto"
        src="https://raw.githubusercontent.com/panchalbhavya2210/svelte-fb-blogs/ae19ecb2d4241abb88ca729b3f600665b04ea392/static/logo.svg"
        alt="Your Company"
      />
      <h2
        class="mt-10 text-center text-2xl font-bold leading-9 tracking-tight text-gray-900"
      >
        Reset password of <span class="text-indigo-600">Byte Blogs.</span>
      </h2>
    </div>

    <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-sm">
      <form class="space-y-6" action="">
        <div>
          <label
            for="email"
            class="block text-sm font-medium leading-6 text-gray-900"
            >Email address</label
          >
          <div class="mt-2">
            <input
              id="email"
              name="email"
              type="email"
              autocomplete="email"
              required
              class="block w-full rounded-md border-0 py-1.5 px-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            />
          </div>
        </div>

        <div>
          <button
            type="submit"
            on:click={sendLink}
            class="flex w-full justify-center rounded-md bg-indigo-600 px-3 py-1.5 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
            >Send link</button
          >
        </div>
      </form>

      <!-- Success Alert -->
      <div
        class="p-4 mb-4 text-sm text-green-800 rounded-lg bg-green-50 dark:bg-white shadow-lg dark:text-green-700 fixed transition {successState
          ? 'translate-y-3 opacity-100'
          : 'translate-y-36 opacity-0'}"
        role="alert"
      >
        <span class="font-medium">Success!</span> Password reset link sent.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
      </div>
      <!-- Error Alert -->
      <div
        class="p-5 mb-2 text-sm text-red-800 rounded-lg bg-red-50 dark:bg-white shadow-lg dark:text-red-700 fixed transition {alertState
          ? 'translate-y-3 opacity-100'
          : 'translate-y-20 opacity-0'}"
        role="alert"
      >
        <span class="font-medium">Oops!</span> Change a few things up and try submitting
        again.
      </div>

      <p class="mt-10 text-center text-sm text-gray-500">
        Changed Password?
        <a
          href="/Login"
          class="font-semibold leading-6 text-indigo-600 hover:text-indigo-500"
          >Login</a
        >
      </p>
    </div>
  </div>
</main>
