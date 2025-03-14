- Install the library using ```npm i express spotify-web-api-node```
  - It is a wrapper for the Spotify Web API, that helps our server.js to interact with the Spotify service, such as fetching user playlists, searching for tracks, or getting artist information.
- Install npm i nodemon --save-dev
  - nodemon monitors your Node.js application's source files. Whenever you make changes, nodemon automatically restarts Node.js server, so you don’t need manual stop and restart.
- then we will require 'express' and 'spotify-web-api-node'. require() only search and load the module when corrsponding lines are executed. The modules loaded by require() are objects. We need to look up object properties.
- complete the express app post. Clearly not a good idea to hard code the secret. We will change it to .env file later,
```
// => {} is a concise way to write functions, called arrow functions. 
// Its role is to separate parameters from the function body, and the function body defines what the function does.
app.post('/login',(req,res)=>{
app.post('/login',(req,res)=>{
    const spotifyApi = new spotifyWebApi({
        redirectUri: 'http://localhost:3000',
        clientId: '2202b07c14004460a03bb92fbcf7aebd',
        clientSecret: 'INSERT_SECRET_FOR_TESTING'
})
})
```
- Once we have the spotify api that takes in these three credentials, we will need pass access code to the request body ```const code = req.body.code;```
- From library docs, we know that then we can call the authorizationCodeGrant, and pass our code, which will return us the data that has the access token, the refresh token, and the token expires time. Set up the authCodeGrant based on library wiki:
```
    spotifyApi.authorizationCodeGrant(code).then(data=>{
        res.json({
            accessToken: data.body.access_token,
            refreshToken: data.body.refresh_token,
            expiresIn: data.body.expires_in
        })
    }
    ).catch(() => {
        res.sendStatus(400);
    })
})
```



