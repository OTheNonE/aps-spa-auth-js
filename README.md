# Autodesk Platform Services | Single-Page Applications | Authorization Client
A JS-SDK for gaining access to Autodesk Platform Services within Single-Page Applications (inspired by [Auth0](https://github.com/auth0/auth0-spa-js)). The client obtains access tokens by using the [PKCE (Proof Key for Code Exchange) authorization flow for public clients](https://aps.autodesk.com/en/docs/oauth/v2/tutorials/get-3-legged-token-pkce/get-3-legged-token-pkce/).

## Getting Started

### Configure your application
Create a Single-Page Application in [Autodesk Platform Services - Applications](https://aps.autodesk.com/hubs/@personal/applications/) and store the Client ID within your application (in a `.env`-file). Specify an authorization callback url to be used in your application (e.g. http://localhost:8000/auth/autodesk/callback or https://example.com/auth/autodesk/callback)

### Installation
Using npm in your project directory run the following command:

```bash
npm install aps-spa-auth-js
```

### Integrate authentication into your application
Create the client and have it globally accessible in your application (verify authorization- and tokenEndpoint urls with [Autodesks documentation](https://aps.autodesk.com/en/docs/oauth/v2/reference)):
```ts
const client = createAPSAuthClient({
    clientId: PUBLIC_AEC_APP_ID,
    authorizationEndpoint: `$https://developer.api.autodesk.com/authentication/v2/authorize`,
    tokenEndpoint: `https://developer.api.autodesk.com/authentication/v2/token`,
    scope: ["data:read"]
})
```

Redirect the user to Autodesks authorization page (create a callback directory for handling the callback and for the authorization page to redirect you back to):
```ts
const redirect_uri = `${window.location.origin}/auth/autodesk/callback`
await client.loginWithRedirect({ redirect_uri })
```

Handle the redirect from the authorization page:
```ts
// https://example.com/auth/autodesk/callback
const { origin, pathname } = $page.url // SvelteKit, holds information about the current url.
const redirect_uri = `${origin}${pathname}`

try {
    await client.handleRedirectCallback({ redirect_uri })
} catch(e) {
    // Handle error
}

goto("/") // (SvelteKit, sends user back to main page.)
```

If the access token expires or becomes invalid, refresh the access token:
```ts
try {
    await client.refreshAccessToken();
} catch(e) {
    // Handle error
}
```

Check out the [aps-spa-auth-js](https://github.com/OTheNonE/aps-spa-auth-js) Github repository for a simple example developed in SvelteKit.

## References
- [aps-spa-auth-js Github Repository](https://github.com/OTheNonE/aps-spa-auth-js)
- [aps-spa-auth-js NPM Package](https://www.npmjs.com/package/aps-spa-auth-js)
- [Autodesk Platform Services - Authentication (OAuth2)](https://aps.autodesk.com/en/docs/oauth/v2/developers_guide/overview/)