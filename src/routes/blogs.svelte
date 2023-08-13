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

  console.log(authFirestore);
  onMount(() => {
    setTimeout(() => {
      const uid = auth.currentUser.uid;
      const refFirestore = collection(authFirestore, "blogs");

      getDocs(refFirestore).then((snapshot) => {
        snapshot.docs.forEach((doc) => {
          blogs.push({ ...doc.data(), id: doc.id });
        });
        console.log(blogs);
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
</script>

<main>
  <div class="bg-white py-24 sm:py-32">
    <div class="mx-auto max-w-7xl px-6 lg:px-8">
      <div class="mx-auto max-w-2xl lg:mx-0">
        <h2 class="text-3xl font-bold tracking-tight text-gray-900 sm:text-4xl">
          From the ByteBlog
        </h2>
        <p class="mt-2 text-lg leading-8 text-gray-600">Just Blog It.</p>
      </div>
      <div
        class="mx-auto mt-10 grid max-w-2xl grid-cols-1 gap-x-8 gap-y-16 border-t border-gray-200 pt-10 sm:mt-16 sm:pt-16 lg:mx-0 lg:max-w-none lg:grid-cols-3"
      >
        {#each blogs as blog}
          <article
            class="flex max-w-xl flex-col items-start justify-between border-2 border-black p-3 rounded-md"
          >
            <span class="hidden">{blogCategory}</span>
            <div class="mb-5 hidden">
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
                  <a href="#">
                    <span class="absolute inset-0" />
                    {blog.blog_owner}
                  </a>
                </p>
                <p class="text-gray-600">Co-Founder / CTO</p>
              </div>
            </div>
          </article>
        {/each}
      </div>
    </div>
  </div>
</main>
