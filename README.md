# Custom Login Page Sample

Here is a simple site that shows how you can build a custom login page to 
IdentityNow apps.  This custom login page will be used for apps that use
federated authentication to IdentityNow, using either SAML or WS-Federation.

This sample contains a simple "portal" page to house links to your
commonly-used apps and a login page that pulls in the IdentityNow JavaScript
library and submits the authentication request.

In order to see this custom login page in action, you will need admin
privileges to your IdentityNow instance, as you will need to configure it to
use your custom login page.
  
This sample uses Node.js, Express, and Handlebars.js.  Although prior
knowledge in these technologies is not required, you should be comfortable in
HTML and JavaScript, as the main usage samples are written using unadorned HTML
and JavaScript.  You are encouraged to take the sample HTML and JavaScript
in `login.hbs` and adapt it to any web server of your choosing.

## Prerequisites

* Node.js v6.4.0 or higher ([https://nodejs.org/en/download/](https://nodejs.org/en/download/))

## Setup

### For Login

1. Download and install Node.js, which also contains npm.
2. Clone this GitHub repository.  The two main files of interest are:
    1. `/views/login.hbs`: a login page, which pulls in and uses functions in
       the IdentityNow JavaScript library
    2. `/views/index.hbs`: a "portal" page containing links to your apps
3. Configure IdentityNow to use the sample login page.  This requires making an
   HTTP request to update your org's configuration.  Here are the basic steps
   using Chrome's Postman extension:
    1. Using Chrome, sign in to IdentityNow as an admin.
    2. Navigate to the Admin tab and strongly authenticate.
    3. Obtain the CSRF token for your session.
        1. While in IdentityNow, open Chrome's Developer tools (open the Chrome
           menu at the top-right of your browser window, then select More
           Tools > Developer Tools).
        2. At the prompt in the Console panel, enter` SLPT.CSRFToken`.  The
           CSRF token for your session will be returned.
    4. Open Chrome's Postman extension.
    5. Send an HTTP POST request to `<baseUrl>/api/org/set` with request
       headers of `Content-Type: application/json` and `X-CSRF-Token: <token>`
       (token = your session's CSRF token), and a body of
       `{"loginUrl": "http://localhost:3000/login"}`.  3000 is the default
       port, defined in `/bin/www`.
    6. To verify your settings, send an HTTP GET request to
       `<baseUrl>/api/org/get`.
4. Configure one or more federated-authentication apps within IdentityNow
   (Admin > Application).
5. Populate the "favorite apps" within this sample portal.  For apps in
   IdentityNow, copy  one or more links into an `<a href="">` tag in
   `/views/index.hbs` to serve as your favorite apps within your portal.
    1. Sign in to IdentityNow and add any number of apps to your launchpad.
    2. Start your browser's Developer Tools (a.k.a. Debugger), select its
       Network tab, and while on your IdentityNow launchpad, refresh the
       browser.
    3. Within the Network tab, find the HTTP GET request to
       `api/app/list?filter=added` (along with a few more query parameter).
       Click to select the entry and examine its response body.
    4. The response body is a JSON array of objects, each representing a
       launcher on your launchpad.  Within each object, the `url` attribute
       can be copied into your sample portal's `<a href="">` value.  If a
       launcher has alternate links, they are found in the `links` attribute.
    5. Collect and copy over as many app `url` values to the sample portal
       as you wish.  You can also add these to your browser's bookmarks to
       directly invoke the app without starting from a portal or launchpad.
6. Start the sample site.  Run `npm install` once to install dependent modules,
   then `npm start` thereafter to start the server.
7. Open your browser to `http://localhost:3000` to bring up the portal and
   click on a link to open one of your apps.  If configured properly, you should
   be redirected to your login page (at `http://localhost:3000/login`).  If you
   enter valid IdentityNow credentials, you should gain access to your app.

### For Logout

1. `/views/index.hbs` contains an example of how to log out of the current
   session by navigating to `<baseUrl>/logout`.  This optionally takes a `goto`
   query parameter, of a URL to redirect you to after logout has completed.
2. This sample shows a `goto` value of `http://localhost:3000`.  So logging
   out will redirect you back to the portal page.  In order for the `goto` URL
   value to be honored, you must register the URL pattern in IdentityNow
   (Admin > Global > Security Settings > Redirect Patterns).  For details,
   refer to this document on Compass:
   [Redirect Patterns](https://community.sailpoint.com/docs/DOC-6786)
3. Once configured, you can log out by clicking the link.

## Notes

* For simplicity, this sample illustrates open access to a portal, requiring
  authentication to its contained links.  If you require your users to log in
  in order to access to your main portal site, you'll need to set up your portal
  to use federated authentication and configure it in IdentityNow.  That
  configuration is outside of the scope of this sample.

* Although you can only configure a single custom login URL per org instance,
  the URL is to an HTTP endpoint that you will host.  When redirecting to your
  login page URL, the org name (script name) and goto URL are made available as
  query parameters.  Therefore, if you wish to show different login pages
  (views) depending on the value of the org name or goto URL, use an MVC
  framework where the controller can evaluate the values of the org and goto
  query parameters and choose a login view accordingly.

## Commands

* `npm install` - Fetches and updates dependent modules.  Run once initially.
* `npm start` - Starts the site running on port 3000.  See `/bin/www` to change.

## Versions
* 1.0.0 - Initial.
* 1.0.1 - login.hbs edited for clarity, using an IIFE following best practice.
* 1.0.2 - README.md step 6 updated with simpler steps to fetch launcher URLs.
API reference within JS library called out in login.hbs.  No code changes.
* 1.0.3 - Fix gotoUrl -> goto default values in login.hbs comments.  No code
changes.
* 1.1.0 - Updated to the 2.0.0 version of the JS library, which is required
to make a CORS request.  Removed the baseUrl value.

Copyright (c) 2016 SailPoint Technologies, Inc.  All rights reserved.
