## Setting up authentication with Google on Google Developers console

Open your Google Developers console. Go to

```
https://console.developers.google.com
```

and select the project (anchor: top-left corner of the screen).

After that, look up for "Credentials" in the search field, or simply go to

```
https://console.developers.google.com/apis/credentials/consent?createClient
```

If you haven't done this before, then you'll be prompted to create an _OAuth consent screen_. It's a dialog that is shown to the user right after they clicked the authentication link. In this dialog, the user can see what permissions the app asks them to give, and two buttons to deny or accept the request.

After you created a consent screen, you'll be asked to create _client ID_. It's precisely what you're there for! Choose the right application type, in most cases, it's **Web application**.

For the Web application setup, the most important settings are **Authorized JavaScript origins** and **Authorized redirect URIs**. For the former, if your app runs on http://localhost:8080 on your local machine, put precisely this address. For the latter, use something like http://localhost:8080/auth/google or just http://localhost:8080/auth (or both).

After you create new credentials, you'll see dialog showing your new **client ID** and **client secret**. That's about 1/4 of the whole process of setting up the authentication.

Next is your browser app.

## Front-end app

On the front-end, what user sees is just a link to Google website. It works precisely this way: just an `a` element with `href` attribute set to a certain URL that leads to Google.

Remember you have client ID and client secret? The front-end app only has to know the former. The whole element looks like this:

```javascript
const GoogleAuthenticationButton = (clientId, redirectUri) => (
  <a href={`https://accounts.google.com/o/oauth2/v2/auth?response_type=token&client_id=${clientId}&redirect_uri=${redirectUri}&scope=email%20profile`}>
    Sign in with Google
  </a>
);
```

Let's say `cliendId` is your client ID and `redirectUri` is http://localhost:8080/auth/google.

The user clicks this link, and Google Accounts page opens, where the user sees consent screen asking them to share email and profile information. Pretty much all you need on the front-end.

1/2 done. The next part is the server.

## Server route

Upon authentication, both successful and not, Google will redirect to the URL that you assigned to "Authorized redirect URIs" setting on the Google Developers console. So, the back-end has to be ready for it:

```javascript
app.get('/auth/google', (request, response) => {
  // In case of React with react-router and Browser History enabled, simply send index.html file.
});
```

So, it's 3/4 done already. The last step is front-end part again.

## Front-end app, again

When the user confirms authentication with Google, they are redirected back to the URL you provided as `redirect_uri` querystring parameter, as in this example, http://localhost:8080/auth/google. The whole URL contains hash:

```
http://localhost:8080/auth/google#access_token=YA29_Ci_SAwPq7Ooq_VQ4B6geGn2obYm4DL1kraZpkolBurD8f1pS2BBqOR7yBggyJ8v_NQ&token_type=Bearer&expires_in=3600
```

In your app, you need to split this hash into three parts: access token, token type and expiration time diff in seconds. That's simple:

```javascript
window.location.hash.replace(/^#/, '').split('&').map(e => e.split('='))
```

The output is an array of arrays of strings:

```json
[["access_token", "YA29_Ci_SAwPq7Ooq_VQ4B6geGn2obYm4DL1kraZpkolBurD8f1pS2BBqOR7yBggyJ8v_NQ"], ["token_type", "Bearer"], ["expires_in", "3600"]]
```

If you need it in form of plain-object, use Array#reduce:

```javascript
const authenticationResults = window.location.hash.replace(/^#/, '').split('&').map(e => e.split('=')).reduce((total, current) => Object.assign({}, total, {[current[0]]: current[1]}), {})
```

and take access token as property of it:

```javascript
authenticationResults.access_token
```

That's it. Now you can use this access token for as long as it is valid.
