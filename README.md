![image](https://github.com/user-attachments/assets/0fbf9536-f1fa-4cae-8273-094f3f8d1f21)# SpotifyReact
A starting project to build a Spotify using React. It creates a Spotify clone that not only has many of Spotify's features but also includes lyric lookup for any song you want.


## Initialization
To begin with, create a new React project with no configuration:
  1. Install Node.js from Node.js website
  2. Create a "SpotifyReact" dirctory and open it using VS code.
  3. Create a basic react project by running the following command in VS code terminal
     
     ```npx create-react-app client```
  5. Create a server dirctory
  6. cd to our client dir in terminal, and run ```npm start``` to view client in browser in http://localhost:3000

Next, we need reate a new spotify app, follow the documentation [here](https://developer.spotify.com/documentation/web-api/concepts/apps). You can set the redirect url in the setting to http://localhost:3000 since we are in dev stage now. 

## Authorization Setup
Learn more about authorization [here](https://developer.spotify.com/documentation/web-api/concepts/authorization)

![image](https://github.com/user-attachments/assets/fae6d95a-5349-44a8-9ddf-973701410f70)

The first step is to request authorization from the user so that the app can access to the Spotify resources on the user's behalf. To do this, our application must build and send a GET request to the /authorize endpoint with the following parameters:
  1. **client_id**	The Client ID generated after registering our application.
  2. **response_type**	Set to code.
  3. **redirect_uri**	The URI to redirect to after the user grants or denies permission.
  4. state	Optional	This provides protection against attacks such as cross-site request forgery.
  5. scope	Optional	If no scopes are specified, authorization will be granted only to access publicly available information
  6. show_dialog	Optional Whether or not to force the user to approve the app again

Next, setup the authorization based on documentations. You can skip my [detailed walkthrough](https://github.com/1996LimingWei/SpotifyReact/blob/d13b6d33703ab9844b48cb95d4ec1dbcc9dd7efd/AuthorizationSetupDetails.md) if you are already familar with it.

After successful setup, we observe the auth code inside the url in the browser window:
http://localhost:3000/?code=Your_code_is_here. Next we need to convert this code to the access token, for the user to authenticate them for different request.

## Server Login Route

We use a library called [spotify Web Api Node](https://www.npmjs.com/package/spotify-web-api-node) that makes everything easier.

Read the authorization section, we need three pieces of info to get authorized:
  1. The token expires time
  2. The access token
  3. The refresh token

We need to do this on the server because it requires our client a secret, which we can get from spotify developer dashboard.

So our next task is to set up a server that we can use for login code, we can start by creating a server.js in the server dir. 

Next, init the package.json by running ```cd ../server/``` and ```npm init -y```

Follow the [detailed documentation](https://github.com/1996LimingWei/SpotifyReact/blob/c37e576c3d7f160f458fe595f4b518e9da9f95ac/ServerLoginRoute.md) to install various dependencies and server setup. 

## useAuth Hook

Next, we need to get the access code from url everytime we access the browser page, and use that for authorization. 

Follow the [detailed documentation](https://github.com/1996LimingWei/SpotifyReact/blob/fcc8c4960f49234c5b1372608289d5287b3eb9f4/UseAuthHook.md) to setup useAuth hook. We return the access token in useAuth, which is essential to call all different spotify apis. But it only last for an hour

## Refresh Token Automatically

Of course we dont want the user to click refresh every hour, we want server to refresh for us. And on the client side, we can set up the access code to call the server.

Follow the [detailed documentation]() to setup useAuth hook. 
