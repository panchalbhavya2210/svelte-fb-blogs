<script>
  import "./global.css";
  import { onMount } from "svelte";
  import { fade, fly } from "svelte/transition";
  import { getAuth } from "firebase/auth";

  import { initializeApp } from "firebase/app";
  import {
    getFirestore,
    collection,
    getDocs,
    updateDoc,
    doc,
  } from "firebase/firestore";
  let userName;
  let userImage;
  let blogDetail;
  let blogDate;
  let blogCategory;
  let blogTitle;
  import { marked } from "marked";

  let blogs = [];

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
  const authFirestore = getFirestore(app);
  let loader;
  let isEdited;

  onMount(() => {
    setTimeout(() => {
      const refFirestore = collection(authFirestore, "blogs");
      getDocs(refFirestore).then((snapshot) => {
        loader = !loader;
        snapshot.docs.forEach((doc) => {
          blogs.push({ ...doc.data(), id: doc.id });
        });
        for (let i = 0; i < blogs.length; i++) {
          userImage = blogs[i].owner_pp;
          blogCategory = blogs[i].blog_categ;
          blogDate = blogs[i].blog_date;
          blogDetail = blogs[i].blog_details;
          userName = blogs[i].blog_owner;
          blogTitle = blogs[i].blog_title;
        }
console.log(blogs)
      });
    }, 1500);
  });
  let modalUserimage;
  let modalUserName;
  let modalBlogTitle;
  let modalBlogImage;
  let modalBlogSummary = "";
  let modalBlogDate;
  let modalBlogCat;
  let blogShower;
  let modalViewCount;
  function handlePriceSelection(blog) {
    blogShower = !blogShower;
    modalUserimage = blog.owner_pp;
    modalUserName = blog.blog_owner;
    modalBlogTitle = blog.blog_title;
    modalBlogImage = blog.blog_img;
    modalBlogSummary = blog.blog_details;
    modalBlogDate = blog.blog_date;
    modalBlogCat = blog.blog_categ;
    modalViewCount = blog.view_count;
    // View Count
    let plusView = modalViewCount + 1;
    const refFirestore = doc(authFirestore, "blogs", blog.id);
    updateDoc(refFirestore, {
      view_count: plusView,
    }).then(() => {});
    const auth = getAuth(app);

    let userId = auth.currentUser.uid;
    const refFirestoreFor = doc(authFirestore, `${userId}/${blog.id}`);
    updateDoc(refFirestoreFor, {
      view_count: plusView,
    }).then(() => {});
  }

  function closeModal() {
    blogShower = !blogShower;
  }
</script>

<main>
  <div
    class="blogPopUp h-screen w-screen z-50 mx-auto backdrop-blur-sm fixed top-0 {blogShower
      ? 'block'
      : 'hidden'}"
    transition:fly={{ y: -200 }}
  >
    <div
      class="subContainer w-full h-full bg-white shadow-lg rounded-lg overflow-y-scroll relative top-0"
    >
      <div class="ownerDet mt-2 flex h-20 items-center">
        <div class="imageCont">
          <img src={modalUserimage} alt="" class="rounded-full w-12 h-12 m-3" />
        </div>
        <div class="ownerDetail">
          <h2 class="font-bold">{modalUserName}</h2>
          <p class="text-gray-700">{modalBlogDate}</p>
        </div>

        <div class="closeBtn absolute right-10">
          <button on:click={closeModal} class="bg-gray-200 p-3 rounded-full"
            ><svg
              xmlns="http://www.w3.org/2000/svg"
              class="icon icon-tabler icon-tabler-minimize"
              width="24"
              height="24"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
              fill="none"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path stroke="none" d="M0 0h24v24H0z" fill="none" />
              <path d="M15 19v-2a2 2 0 0 1 2 -2h2" />
              <path d="M15 5v2a2 2 0 0 0 2 2h2" />
              <path d="M5 15h2a2 2 0 0 1 2 2v2" />
              <path d="M5 9h2a2 2 0 0 0 2 -2v-2" />
            </svg></button
          >
        </div>
      </div>

      <div class="blogTitle ml-3 mt-4 mr-3">
        <p class="font-bold text-3xl">{modalBlogTitle}</p>
      </div>

      <div class="blogImage m-4 flex justify-center">
        <img
          src={modalBlogImage}
          alt=""
          srcset=""
          class="w-full h-full rounded-md"
        />
      </div>

      <div class="p-6 liststyle">
        {@html marked(modalBlogSummary)}
      </div>
    </div>
  </div>

  <div class="bg-white py-24 sm:py-32">
    <div class="mx-auto max-w-7xl px-6 lg:px-8">
      <div class="mx-auto max-w-2xl lg:mx-0">
        <h2 class="text-3xl font-bold tracking-tight text-gray-900 sm:text-4xl">
          From the ByteBlog.
        </h2>
        <p class="mt-2 text-lg leading-8 text-gray-600">Just Blog It.</p>

        <div class="loader absolute {loader ? 'hidden' : 'block'}">
          <div role="status ">
            <svg
              aria-hidden="true"
              class="w-8 h-8 mr-2 text-gray-200 animate-spin dark:text-gray-100 fill-indigo-600"
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
            <span class="sr-only text-black">Loading...</span>
          </div>
        </div>
      </div>
      <div
        class="mx-auto mt-10 grid max-w-2xl grid-cols-1 gap-x-8 gap-y-16 border-t border-gray-200 pt-10 sm:mt-16 sm:pt-16 lg:mx-0 lg:max-w-none lg:grid-cols-3"
      >
        {#each blogs as blog}
          <article
            class="flex max-w-xl flex-col items-start justify-between shadow-lg p-3 rounded-md"
          >
            <span class="hidden">{blogCategory}</span>
            <div class="mb-5 w-full">
              <img
                src={blog.blog_img}
                alt=""
                class="object-cover h-72 w-full rounded-md"
              />
            </div>

            <time datetime="2020-03-16" class="text-gray-500 text-xs"
              >{blog.blog_date}</time
            >
            <div class="flex items-center gap-x-4 text-xs">
              <div
                class="relative z-10 rounded-full bg-gray-50 px-3 py-1.5 font-medium text-gray-600 hover:bg-gray-100"
              >
                {blog.blog_categ}
              </div>
              <div
                class="relative z-10 rounded-full bg-gray-50 px-3 py-1.5 font-medium text-gray-600 hover:bg-gray-100"
              >
                {blog.isEdited ? "Edited" : "Not Edited"}
              </div>
              <div
                class="relative z-10 flex items-center rounded-full bg-gray-200 px-3 py-1.5 font-medium text-gray-600 hover:bg-gray-100"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon icon-tabler icon-tabler-eye"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  stroke-width="2"
                  stroke="currentColor"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                  <path d="M10 12a2 2 0 1 0 4 0a2 2 0 0 0 -4 0" />
                  <path
                    d="M21 12c-2.4 4 -5.4 6 -9 6c-3.6 0 -6.6 -2 -9 -6c2.4 -4 5.4 -6 9 -6c3.6 0 6.6 2 9 6"
                  />
                </svg>
                <div class="m-1">{blog.view_count}</div>
              </div>
            </div>
            <div class="group relative">
              <h3
                class="mt-3 text-lg font-semibold leading-6 text-gray-900 group-hover:text-gray-600"
              >
                <a href="#">
                  <span class="absolute inset-0" />
                  {blog.blog_title}
                </a>
              </h3>
              <p class="mt-5 line-clamp-3 text-sm leading-6 text-gray-600">
                {blog.blog_details}
              </p>
            </div>
            <div class="relative mt-8 flex items-center gap-x-4 w-full">
              <img
                src={blog.owner_pp}
                alt=""
                class="h-10 w-10 rounded-full bg-gray-50"
              />
              <div class="text-sm leading-6">
                <p class="font-semibold text-gray-900">
                  <span class="absolute inset-0" />
                  {blog.blog_owner}
                </p>
              </div>
              <button
                class="absolute right-3 p-2 rounded-full bg-gray-200 text-sm"
                on:click={() => handlePriceSelection(blog)}
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon icon-tabler icon-tabler-maximize"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  stroke-width="2"
                  stroke="currentColor"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                  <path d="M4 8v-2a2 2 0 0 1 2 -2h2" />
                  <path d="M4 16v2a2 2 0 0 0 2 2h2" />
                  <path d="M16 4h2a2 2 0 0 1 2 2v2" />
                  <path d="M16 20h2a2 2 0 0 0 2 -2v-2" />
                </svg>
              </button>
            </div>
          </article>
        {/each}
      </div>
    </div>
  </div>
</main>

<style>
  /* .no-tailwind {
    all: unset;
  } */
</style>
