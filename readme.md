# Overview

Sample Single-Page Web App (SPA) for Okta OpenID Connect (OIDC)

## Sample Scenarios

### OpenID Connect with Custom UI

You can find the main javascript code in `/js/oidc-app.js` and html in `oidc.html`

This sample demonstrates the OpenID Connect implicit flow:

- Sign-In: Authenticates user with [name/password](http://developer.okta.com/docs/api/resources/authn.html#primary-authentication-with-public-application) and exchanges a [sessionToken](http://developer.okta.com/docs/api/resources/authn.html#session-token) for an id_token (JWT) using a hidden iframe
- Refresh: Attempts to use an existing session to obtain an id_token (JWT) using a hidden iframe

These scenarios are enabled by the `okta_post_message` custom `response_mode` for the [OpenID Connect Authentication Request](http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) which uses [HTML5 Window Messaging] (https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) and a hidden iframe to return the [id_token]  (http://openid.net/specs/openid-connect-core-1_0.html#AuthResponse) to the Single Page Web App (SPA) without refreshing or redirecting the page.

See [postMessageCallback](https://github.com/mcguinness/okta-oidc-sample/blob/master/js/OktaAuthRequireJquery.js#L1118) for implementation details of how the `okta_post_message` response_mode works

#### OpenID Connect with Okta Sign-In Widget

You can find the main javascript code and html in `widget.html`

> The Okta Sign-In Widget is currently not working and waiting for some product changes

## Setup

> This document assumes you host this app on `http://localhost:8080/`

1. Grant the app [CORS access](http://developer.okta.com/docs/api/getting_started/enabling_cors.html) in your Okta organization

2. Create OpenID Connect Application in the Okta Admin UI

    1. Applications>Add Application
    2. Click the "Create New App" button
    3. Select "OpenID Connect" and click the "Create" button
    4. Enter a name for the app such as "Sample OIDC App", select "Browser" as 5. App Type, and click "Next"
    6. Add the following redirect URIs and click "Finish"
        - "http://localhost:8080/oidc"
        - "http://localhost:8080/oidc.html"
        - "http://localhost:8080/widget"
        - "http://localhost:8080/widget.html"
    7. Navigate to the Groups tab for the new app and assign the everyone group

3. Update `/js/oidc-app.js` with `client_id` from your OIDC Application and your Okta organization url

    ```
      var client = new OktaAuth({
        uri: "https://org.oktapreview.com",
        clientId: 'IaBv2P521nkEC8IzaL45',
        redirectUri: window.location.href
      });
    ```

    > This example assumes that the `redirectUri` is the url that the page is hosted on and matches the same value as the OAuth 2.0 Client Registration `redirect_uris`

4. Update `widget.html` with `client_id` from your OIDC Application and your Okta organization url

    ```
    var oktaSignIn = new OktaSignIn({
        baseUrl: 'https://example.oktapreview.com',
        clientId: 'IaBv2P521nkEC8IzaL45',
        logo: '/images/acme_logo.png',
    ```

    > This example assumes that the `redirectUri` is the url that the page is hosted on and matches the same value as your OIDC Application Redirect URIs

4. Start Web Server (e.g. http://localhost:8080/oidc.html")

    Quick and easy way to self-host is by installing [node.js](https://nodejs.org/en/download/) and the [http-server](https://www.npmjs.com/package/http-server) package which is a simple command-line web server

    ```
    karl@guinness: ~/src/okta-oidc-sample
    $ http-server
    Starting up http-server, serving ./ on: http://0.0.0.0:8080
    Hit CTRL-C to stop the server
    ```

5. Visit http://localhost:8080/oidc to launch the sample app with custom UI

## Social Authentication

The Okta Sign-In Widget also supports Social Authentication.  You need to first add a Social IdP via the Okta Admin UI and obtain the `id` for the IdP which is found in the **Authorize URL** such as https://example.okta.com/oauth2/v1/authorize?idp=**0oabzpziblMwBLLqO0g4**.

When initializing the the widget you then add the IdP as an additional param with the `id` and `type` (e.g. `FACEBOOK`, `GOOGLE`, or `LINKEDIN`) which controls the branding of the button

```
oktaSignIn.renderEl(
{
  idps: [
    {
      type: 'FACEBOOK',
      id: '0oa5kecjfwuF4HQ4w0h7'
    }
  ]
}
```
