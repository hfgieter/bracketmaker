<script>
    import { token, tokenExpired, appUrl } from "./stores.js";

    const client_id = "c197a3ffaa294a6aa5e7715873fdbe95"; // Your client id

    function generateRandomString(length) {
      let text = "";
      const possible =
        "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

      for (let i = 0; i < length; i++) {
        text += possible.charAt(Math.floor(Math.random() * possible.length));
      }
      return text;
    }

    const url = new URL("https://accounts.spotify.com/authorize?");
    const scope = "user-read-private user-read-email user-top-read";
    const state = generateRandomString(16);
    $: params = new URLSearchParams({
      response_type: "token",
      client_id,
      scope,
      redirect_uri: $appUrl,
      state,
    });
    $: loginLink = url + params;
  </script>

  {#if !$token}
    <div id="login">
      <a href={loginLink}>
        <button class="login-btn">Connect to Spotify</button>
      </a>
      <br />
    </div>
  {/if}

  {#if $tokenExpired}
    <section class="expired-token">
      <p>Token expired! Please log out and log back in again.</p>
      <a href={$appUrl}>
        <button>Logout</button>
      </a>
    </section>
  {/if}

  <style>

    #login {
      margin: 2.5rem auto;
    }

    .login-btn {
      margin: .5rem auto;
    }


    .expired-token {
      font-size: 1.25rem;
    }
  </style>