<script>
  import "../global.css";

  import { initializeApp } from "firebase/app";
  import { getAuth } from "firebase/auth";
  import { getDatabase, get, ref } from "firebase/database";
  import {
    getFirestore,
    collection,
    getDocs,
    doc,
    updateDoc,
    deleteDoc,
  } from "firebase/firestore";
  import { onMount } from "svelte";
  let blogs = [];

  let currentDate = new Date();

  let month = currentDate.getMonth() + 1;
  let date = currentDate.getDate();
  let year = currentDate.getFullYear();

  let fullFormation = date + "/" + month + "/" + year;

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
  let profileImage;
  let userEmail;
  let userName;
  let userImage;
  let blogDetail;
  let blogDate;
  let blogCategory;
  let loader;
  let stateOfUpdate;

  onMount(() => {
    setTimeout(() => {
      let userId = auth.currentUser.uid;
      get(ref(getDb, userId))
        .then((data) => {
          profileImage = data.val().userUrl;
          userName = data.val().userName;
          userEmail = data.val().userEmail;
        })
        .then(() => {
          const refFirestore = collection(authFirestore, userId);
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
        });
    }, 1500);
  });

  let blogTitle;
  let blogSummaryUpd;
  let blogCategoryUpd;
  let isEdited;
  let editorState;
  let blogId;
  function handleBlogSelection(blog) {
    blogTitle = blog.blog_title;
    blogSummaryUpd = blog.blog_details;
    blogCategoryUpd = blog.blog_categ;
    editorState = !editorState;
    blogId = blog.id;
  }

  function handleUpdate() {
    const refFirestore = doc(authFirestore, "blogs", blogId);
    stateOfUpdate = !stateOfUpdate;
    updateDoc(refFirestore, {
      isEdited: true,
      blog_title: blogTitle,
      blog_details: blogSummaryUpd,
      blog_categ: blogCategoryUpd,
      blog_date: fullFormation,
    }).then(() => {});

    const auth = getAuth(app);
    let userId = auth.currentUser.uid;
    const refFirestoreFor = doc(authFirestore, `${userId}/${blogId}`);
    updateDoc(refFirestoreFor, {
      isEdited: true,
      blog_title: blogTitle,
      blog_details: blogSummaryUpd,
      blog_categ: blogCategoryUpd,
      blog_date: fullFormation,
    }).then(() => {
      stateOfUpdate = !stateOfUpdate;
    });
  }
  let deleteState;
  function deleteMyBlog(blog) {
    const auth = getAuth(app);
    let userId = auth.currentUser.uid;
    deleteDoc(doc(authFirestore, `${userId}/${blog.id}`)).then(() => {});
    deleteDoc(doc(authFirestore, `blogs/${blog.id}`)).then(() => {
      deleteState = !deleteState;
    });
  }
</script>

<main class="relative top-16">
  <div class="editorForm">
    <div
      class="formOfEditor fixed w-full z-20 top-20 {editorState
        ? 'block'
        : 'hidden'}"
    >
      <div
        class="relative m-auto sm:mx-auto sm:w-full sm:max-w-sm overflow-hidden z-20 bg-white p-5 shadow-lg rounded-sm"
      >
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
                bind:value={blogSummaryUpd}
                col="1000"
                class="block w-full rounded-md border-0 py-1.5 px-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
              />
            </div>
          </div>
          <div>
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
                  bind:value={blogCategoryUpd}
                  class="block w-full rounded-md border-0 py-1.5 px-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                />
              </div>
            </div>

            <div>
              <button
                type="submit"
                id="btn"
                on:click={handleUpdate}
                class="flex w-full justify-center mt-3 rounded-md bg-indigo-600 px-3 py-1.5 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
              >
                <svg
                  aria-hidden="true"
                  class="mt-1 w-4 h-4 mr-3 text-gray-200 animate-spin dark:text-indigo-600 fill-slate-100 {stateOfUpdate
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
                {stateOfUpdate ? "Updating Blog" : "Update Blog"}
              </button>
              <div>
                <button
                  on:click={() => {
                    editorState = !editorState;
                  }}
                  class="flex w-full justify-center mt-3 rounded-md bg-white border-2 text-indigo-500 border-indigo-500 px-3 py-1.5 text-sm font-semibold leading-6 shadow-sm focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
                >
                  Close
                </button>
              </div>
            </div>
          </div>
        </form>
      </div>
    </div>

    <div
      class="flexImgDetail h-80 bg-[url('https://wallpaperaccess.com/full/8351171.gif')] relative"
    >
      <div class="imageContainer flex justify-center items-center">
        <img
          src={profileImage}
          alt="profileImage"
          class="w-36 h-36 rounded-full relative top-5"
        />
      </div>
      <div class="flex justify-center items-center mt-7">
        <h1>{userName}</h1>
      </div>
      <div class="flex justify-center items-center mt-2">
        <p>{userEmail}</p>
      </div>
    </div>

    <!-- <div
    class="blogPopUp h-screen w-screen z-50 flex justify-center mx-auto backdrop-blur-sm p-3 fixed top-0 {blogShower
      ? 'block'
      : 'hidden'}"
  >
    <div
      class="subContainer w-11/12 bg-white shadow-lg m-5 rounded-lg border-solid border-2 border-black overflow-y-scroll relative top-0"
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

      <div class="p-6">
        {@html marked(modalBlogSummary)}
      </div>
    </div>
  </div> -->

    <div class="bg-white py-24 sm:py-32">
      <div class="mx-auto max-w-7xl px-6 lg:px-8">
        <div class="mx-auto max-w-2xl lg:mx-0">
          <h2
            class="text-3xl font-bold tracking-tight text-gray-900 sm:text-4xl"
          >
            Blogs Created By You.
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
              <div class="flex items-center gap-x-4 text-xs">
                <time datetime="2020-03-16" class="text-gray-500"
                  >{blog.blog_date}</time
                >
                <div
                  class="relative z-10 rounded-full bg-gray-50 px-3 py-1.5 font-medium text-gray-600 hover:bg-gray-100"
                >
                  {blog.blog_categ}
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
                  class="absolute right-16 p-2 rounded-full bg-gray-200 text-sm"
                  on:click={() => deleteMyBlog(blog)}
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="icon icon-tabler icon-tabler-trash"
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
                    <path d="M4 7l16 0" />
                    <path d="M10 11l0 6" />
                    <path d="M14 11l0 6" />
                    <path d="M5 7l1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2 -2l1 -12" />
                    <path d="M9 7v-3a1 1 0 0 1 1 -1h4a1 1 0 0 1 1 1v3" />
                  </svg>
                </button>
                <button
                  class="absolute right-3 p-2 rounded-full bg-gray-200 text-sm"
                  on:click={() => handleBlogSelection(blog)}
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="icon icon-tabler icon-tabler-edit-circle"
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
                    <path
                      d="M12 15l8.385 -8.415a2.1 2.1 0 0 0 -2.97 -2.97l-8.415 8.385v3h3z"
                    />
                    <path d="M16 5l3 3" />
                    <path d="M9 7.07a7 7 0 0 0 1 13.93a7 7 0 0 0 6.929 -6" />
                  </svg>
                </button>
              </div>
            </article>
          {/each}
        </div>
      </div>
      <div class="relative">
        <div
          class="p-4 mb-4 text-sm text-green-800 rounded-lg bg-green-50 dark:bg-red-100 shadow-lg dark:text-red-900 transition fixed bottom-0 {deleteState
            ? 'translate-y-0 opacity-100'
            : 'translate-y-36 opacity-0'}"
          role="alert"
        >
          <span class="font-medium">Success!</span> Your blog is deleted. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        </div>
      </div>
    </div>
  </div>
</main>
