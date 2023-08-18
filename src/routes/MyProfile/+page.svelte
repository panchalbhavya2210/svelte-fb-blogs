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

  let profileImage;
  let userName;
  let userEmail;
  function readDb() {
    let userId = auth.currentUser.uid;
    get(ref(getDb, userId)).then((data) => {
      profileImage = data.val().userUrl;
      userName = data.val().userName;
      userEmail = data.val().userEmail;
    });
  }

  readDb();
</script>

<main class="relative top-16">
  <div
    class="flexImgDetail h-72 bg-[url('https://wallpaperaccess.com/full/8351171.gif')]"
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
</main>
