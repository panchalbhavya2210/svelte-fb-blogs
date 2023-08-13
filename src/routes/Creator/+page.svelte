<script>
  import { initializeApp } from "firebase/app";
  import { getAuth } from "firebase/auth";
  import { getDatabase, get, ref } from "firebase/database";
  import {
    getStorage,
    ref as sRef,
    uploadBytesResumable,
    getDownloadURL,
  } from "firebase/storage";
  import {
    getFirestore,
    addDoc,
    doc,
    setDoc,
    collection,
  } from "firebase/firestore";
  import { onMount } from "svelte";
  let currentDate = new Date();

  let month = currentDate.getMonth() + 1;
  let date = currentDate.getDate();
  let year = currentDate.getFullYear();

  let fullFormation = date + "/" + month + "/" + year;
  // Add a new document in collection "cities"

  import "../global.css";

  let state;
  let alertState;
  let successState;

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
  const authFirestore = getFirestore(app);
  const storageFile = getStorage(app);

  let blog_category;
  let blogTitle;
  let profileImage;
  let profileName;
  let blogSummary;
  let files;
  let blogImageUrl;

  let progress = 10;

  function runFileUpload() {
    const stRef = sRef(storageFile, `images/${files[0].name}` + files[0]);
    const uploadTask = uploadBytesResumable(stRef, files[0]);
    uploadTask.on(
      "state_changed",
      (snapshot) => {
        progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
        switch (snapshot.state) {
          case "paused":
            break;
          case "running":
            break;
        }
      },
      (error) => {},
      () => {
        getDownloadURL(uploadTask.snapshot.ref).then((downloadURL) => {
          let userId = auth.currentUser.uid;
          get(ref(getDb, userId)).then((data) => {
            profileImage = data.val().userUrl;
            profileName = data.val().userName;
            addDoc(collection(authFirestore, "blogs"), {
              owner_pp: profileImage,
              blog_categ: blog_category,
              blog_date: fullFormation,
              blog_details: blogSummary,
              blog_img: downloadURL,
              blog_owner: profileName,
              blog_title: blogTitle,
            });
          });
        });
      }
    );
  }
  function readDb() {}
</script>

<main class="relative top-10">
  <div class="flex min-h flex-col justify-center px-6 py-12 lg:px-8">
    <div class="mt-1 sm:mx-auto sm:w-full sm:max-w-sm overflow-hidden">
      <form class="space-y-3">
        <div>
          <label
            for="email"
            class="block text-sm font-medium leading-6 text-gray-900"
            >Blog Title</label
          >
          <div class="mt-0">
            <input
              id="email"
              required
              bind:value={blogTitle}
              class="block w-full rounded-md border-0 py-1.5 px-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            />
          </div>
        </div>
        <div>
          <label
            for="username"
            class="block text-sm font-medium leading-6 text-gray-900"
            >Blog Summary</label
          >
          <div class="mt-2">
            <textarea
              id="username"
              name="username"
              required
              col="1000"
              bind:value={blogSummary}
              class="block w-full rounded-md border-0 py-1.5 px-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            />
          </div>
        </div>

        <div>
          <div class="flex items-center justify-between">
            <label
              for="password"
              class="block text-sm font-medium leading-6 text-gray-900"
              >Blog Category</label
            >
          </div>
          <div class="mt-2">
            <input
              id="password"
              required
              bind:value={blog_category}
              class="block w-full rounded-md border-0 py-1.5 px-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            />
          </div>
          <div class="mt-2">
            <div class="flex items-center justify-between">
              <label
                for="file"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Blog Image</label
              >
            </div>
            <input
              type="file"
              class="file:bg-indigo-500 file:text-white file:border-none file:rounded-sm file:p-1 mt-3 mb-3 bg-slate-100 w-96"
              name=""
              id="file"
              bind:files
            />
          </div>
        </div>

        <div>
          <button
            type="submit"
            id="btn"
            on:click={runFileUpload}
            class="flex w-full justify-center rounded-md bg-indigo-600 px-3 py-1.5 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
          >
            <svg
              aria-hidden="true"
              class="mt-1 w-4 h-4 mr-3 text-gray-200 animate-spin dark:text-indigo-600 fill-slate-100 {state
                ? 'flex'
                : 'hidden'}"
              viewBox="0 0 100 101"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                fill="currentColor"
              />
              <path
                d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                fill="currentFill"
              />
            </svg>
            <span class="sr-only">Loading...</span>
            Submit Blog
          </button>
        </div>
      </form>
      <!-- Success Alert -->
      <div class="relative">
        <div
          class="p-4 mb-4 text-sm text-green-800 rounded-lg bg-green-50 dark:bg-green-100 shadow-lg dark:text-green-900 absolute transition {successState
            ? 'translate-y-0 opacity-100'
            : 'translate-y-36 opacity-0'}"
          role="alert"
        >
          <span class="font-medium">Success!</span> Your account is created.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        </div>
      </div>
      <!-- Error Alert -->
      <div class="relative">
        <div
          class="p-5 mb-2 text-sm text-red-800 rounded-lg bg-red-50 dark:bg-red-100 shadow-lg dark:text-red-900 absolute transition {alertState
            ? 'translate-y-0 opacity-100'
            : 'translate-y-20 opacity-0'}"
          role="alert"
        >
          <span class="font-medium">Oops!</span>
        </div>
      </div>
    </div>
  </div>
</main>
