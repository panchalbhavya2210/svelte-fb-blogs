<script>
  import { initializeApp, getApps } from "firebase/app";
  import { getAuth, createUserWithEmailAndPassword } from "firebase/auth";
  import "../global.css";

  const firebaseConfig = {
    apiKey: import.meta.env.VITE_API_KEY,
    authDomain: import.meta.env.VITE_AUTH_DOMAIN,
    projectId: import.meta.env.VITE_PRJ_ID,
    storageBucket: import.meta.env.VITE_STG_BKT,
    messagingSenderId: import.meta.env.VITE_MSI,
    appId: import.meta.env.VITE_APID,
    measurementId: import.meta.env.VITE_MID,
  };
  if (getApps().length == 0) {
    // const app = initializeApp(firebaseConfig);
    // const auth = getAuth(app);
    // let email = "b@gmail.com";
    // let pass = "123456";
    // createUserWithEmailAndPassword(auth, email, pass)
    //   .then((userCred) => {
    //     console.log(userCred);
    //   })
    //   .catch((err) => {
    //     console.log(err);
    //   });
  } else {
    undefined;
  }
</script>

<main>
  <div class="flex min-h-full flex-col justify-center px-6 py-12 lg:px-8">
    <div class="sm:mx-auto sm:w-full sm:max-w-sm">
      <img class="mx-auto h-10 w-auto" src="" alt="Your Company" />
      <h2
        class="mt-10 text-center text-2xl font-bold leading-9 tracking-tight text-gray-900"
      >
        Reset password of <span class="text-indigo-600">Blogging Bytes</span>
      </h2>
    </div>

    <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-sm">
      <form class="space-y-6" action="/home">
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
            class="flex w-full justify-center rounded-md bg-indigo-600 px-3 py-1.5 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
            >Send link</button
          >
        </div>
      </form>

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
