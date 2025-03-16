- Insert another useEffect() at useAuth()'s useEffect, which will run whenever refresh token and expiresIn changes. So we make sure that we refresh it before our token expires.
  ```
      // previous scripts
      useEffect(()=>{
      },[refreshToken,expiresIn])
      return accessToken
  }
  ```
- Refer back to the spotify-web-api-node [doc](https://www.npmjs.com/package/spotify-web-api-node#:~:text=spotifyApi.refreshAccessToken()), there is a spotifyApi.refreshAccessToken(), which can be used here for refrehsh. It can be set in our credentials
