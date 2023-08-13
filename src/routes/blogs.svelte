<script>
  import "./global.css";
  import { onMount } from "svelte";
  import { initializeApp } from "firebase/app";
  import { getAuth } from "firebase/auth";
  import { getFirestore, collection, getDocs } from "firebase/firestore";
  let userName;
  let userImage;
  let blogDetail;
  let blogDate;
  let blogCategory;
  // let blogImage;
  // let blogOwner;
  let blogTitle;
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
  // if (getApps().length == 0) {
  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);
  const authFirestore = getFirestore(app);

  let loader;

  onMount(() => {
    setTimeout(() => {
      const uid = auth.currentUser.uid;
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
      });
    }, 1500);
  });
  let modalUserimage;
  let modalUserName;
  let modalBlogTitle;
  let modalBlogImage;
  let modalBlogSummary;
  let modalBlogDate;
  let modalBlogCat;
  let blogShower;
  function handlePriceSelection(blog) {
    blogShower = !blogShower;
    modalUserimage = blog.owner_pp;
    modalUserName = blog.blog_owner;
    modalBlogTitle = blog.blog_title;
    modalBlogImage = blog.blog_img;
    modalBlogSummary = blog.blog_details;
    modalBlogDate = blog.blog_date;
    modalBlogCat = blog.blog_categ;
  }

  function closeModal() {
    blogShower = !blogShower;
  }
</script>

<main>
  <div
    class="blogPopUp h-screen w-screen z-50 flex justify-center mx-auto backdrop-blur-sm p-3 fixed top-0 {blogShower
      ? 'block'
      : 'hidden'}"
  >
    <div
      class="subContainer w-11/12 bg-white shadow-lg m-5 rounded-lg border-solid border-2 border-black overflow-y-scroll relative sm:overflow-y-hidden top-0"
    >
      <div class="ownerDet mt-2 flex h-20 items-center">
        <div class="imageCont">
          <img src={modalUserimage} alt="" class="rounded-full w-12 h-12 m-3" />
        </div>
        <div class="ownerDetail">
          <h1 class="font-bold">{modalUserName}</h1>
          <p class="text-gray-700">{modalBlogDate}</p>
        </div>

        <div class="closeBtn absolute right-10">
          <button on:click={closeModal}>Close</button>
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
          class="w-80 h-80 rounded-md"
        />
      </div>

      <div class="blogSummary p-5">
        {modalBlogSummary}
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
            class="flex max-w-xl flex-col items-start justify-between border-2 border-black p-3 rounded-md"
          >
            <span class="hidden">{blogCategory}</span>
            <div class="mb-5">
              <img
                src={blog.blog_img}
                alt=""
                class="object-fill h-72 w-96 rounded-md"
              />
            </div>
            <div class="flex items-center gap-x-4 text-xs">
              <time datetime="2020-03-16" class="text-gray-500"
                >{blog.blog_date}</time
              >
              <a
                href="#"
                class="relative z-10 rounded-full bg-gray-50 px-3 py-1.5 font-medium text-gray-600 hover:bg-gray-100"
                >{blog.blog_categ}</a
              >
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
            <div class="relative mt-8 flex items-center gap-x-4">
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
                class="absolute left-44 w-24 bg-gray-200 pt-1 pb-1 pr-1 pl-1 font-bolder rounded-sm text-sm"
                on:click={() => handlePriceSelection(blog)}
              >
                View Blog
              </button>
            </div>
          </article>
        {/each}
      </div>
    </div>
  </div>
</main>
