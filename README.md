# SpotifyReact
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


