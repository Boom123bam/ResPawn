<script>
  import { auth } from "../firebase";
  import Navbar from "../components/Navbar.svelte";
  import "./reset.css";
  import "./styles.css";
  import { userData } from "./userStore";
  import Footer from "../components/Footer.svelte";
  import { page } from "$app/stores";
  import "./layout.css";
  import {
    ReCaptchaV3Provider,
    initializeAppCheck,
  } from "firebase/app-check";
  import { browser } from "$app/environment";
  import { app } from "../firebase";

  auth.onAuthStateChanged(function (user) {
    if (user) {
      const { uid, displayName } = user;
      const data = { uid, displayName };
      userData.set(data);
    } else {
      userData.set(null);
    }
  });

  if (browser) {
    const appCheckOptions = {
      provider: new ReCaptchaV3Provider(
        "6Lcog7YoAAAAADymEbAawFj9qJKYZxEIeuAv_4Pq"
      ),
    };

    if (import.meta.env.DEV) {
      self.FIREBASE_APPCHECK_DEBUG_TOKEN = true;
    }

    // Initialize AppCheck with the options
    initializeAppCheck(app, appCheckOptions);
  }
</script>

<div class="app">
  <header>
    <Navbar />
  </header>

  <main>
    <slot />
  </main>

  {#if !$page.url.pathname.endsWith("play")}
    <footer>
      <Footer />
    </footer>
  {/if}
</div>
