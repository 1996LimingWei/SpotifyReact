- We go back to the App.js in client, and we can see in browser that we have code in our url. Update this line ```const code = new URLSearchParams(window.location.search).get('code')```
  - In a web browser environment, the window object represents the browser window itself, and window.location is an object that contains information about the current page's URL.
  - The search property of the window.location object returns a string that contains the query string part of the URL. URLSearchParams parses the query string and organizes the key - value pairs into an object - like structure. After creating the URLSearchParams object, it internally stores the key - value pairs such as code: "12345" and state: "abc".
- Now, let's finally create Dashboard.js in the client dir and use rfc to generate the content.
  - let's have the code print out on the dashboard for easier observation for now
    ```
    export default function Dashboard({ code }) {
      return (
        <div>{code}</div>
      )
    }
    ```
- In App.js, if we have a code, then we want to render out the dashboard and pass in the code. Otherwise we just render this login component(let user login again)
  ```
  function App() {
    return code? <Dashboard code={code}/> : <Login />
  }
  ```
- Now, instead of putting all the logic for our login and authentication of therefresh token in our dashboard, let’s create a custom hook that handles all this logic.
  - Hooks enable some features of React without writing classes, adding more capabilities to functional components.
- Create a useAuth.js under src dir with rfc again. It will store the information that we need: access token, refresh token, and expiry. So, three differernt things we are returning from our server when we login, and we need to get state for all those.
  ```
  const[accessToken, setAccessToken] = useState()
  const[refreshToken, setRefreshToken] = useState()
  const[expiresIn, setExpiresIn] = useState()
  ```
  - useState() is a Hook function provided by React, which is used to add state to functional components. It returns an array containing two elements. This allows the elements in the array (or object) to be destructured and assigned to accessToken (used to store the access token) and setAccessToken (used to update the state of the access token).

- Now we have place to store, but in order to get these info, we need a simple use effect, which is also a hook. By having [code] as the dependency function, we can have this useEffect run everytime code is changed.
  - useEffect accepts two parameters:
    - Callback function: This function contains the specific side-effect logic. For example, operations like making API requests to fetch data, adding event listeners, etc., are written here.
    - Dependency array (optional): It is an array containing dependencies.
  - useEffect determines when to re-run the callback function based on changes in the dependency array. If the dependency array is omitted, that is, written as useEffect(() => {/* Side-effect logic */});, then the callback function of useEffect will just execute after each component rendering is completed.
  - Also, useEffect has a cleanup function, which is used to clear side-effects when the component is unmounted or when the dependencies are updated.

- Next, we use axios in order to call the api, because it's easier than using fetch. Run ```cd ../client/``` and ```npm i axios ```
  - Axios is a Promise-based HTTP client that can be used in both browser and Node.js environments. It is mainly used to send HTTP requests to servers, such as GET, POST, PUT, DELETE, etc. Compared with the native fetch API, it has the following advantages:
    - Concise and easy: It provides a more concise and intuitive API, increase readability.
    - Interceptors: It can intercept and process requests before they are sent and responses after they are returned, which is convenient for global request and response processing, such as adding request headers and handling errors.
    - Promise support: It returns a Promise object, which facilitates the use of then and catch for chained calls to handle asynchronous operations.
      - Asynchronous operations are tasks that don't block the execution of subsequent code in a program. They let a program carry on with other code while a time-consuming task, like a network request (e.g., with Axios), file reading, or a timer operation (using setTimeout), is in progress.

- If we go back to the server.js, we see that we are not running our application on any port. What does it mean?
  - A port is a number-based identifier that is used to distinguish different applications running on a computer. It's like a specific "door" through which data can flow in and out of a particular process. Each port number is unique within a given system.
- So we add app.listen(3001) in server.js for now. You might wonder, why do we have redirect uri of 3000, but listen to 3001 in server.js? 
  - When a server application "listens" to a port, it means that the application is configured to wait for incoming network connections on that specific port. Like the server is "sitting by the door" and waiting for clients to send requests.
  - The client (running on port 3000) is responsible for the user interface and handling user interactions. In this case, 3000 is the address of our React application (client-side). When the user clicks the "Login with Spotify" button, they are redirected to the Spotify authorization page. After authorizing, Spotify sends them back to our React app with an authorization code in the URL query parameters.
  - The server (server.js) is responsible for handling API requests and communicating with the Spotify Web API. It runs on a different port (3001) to separate the concerns of the client and the server. This allows for better organization and scalability.
  - 
- In useAuth.js's useAuth, at port 3001, we want to go to login route and post our code. It will call all the code on our server. After post the code, we received either pending, fulfilled, or reject. And then we use .then to log what is returned.
  ```
      useEffect(() => {
          axios.post('http://localhost:3001/login',{
              code,
          }).then(res => {console.log(res.data)})
      }, [code])
  ```
- Next, we cd to server again and run it with nodemod.
- Make sure we pull in the useAuth to dashboard.js ```const accessToken = useAuth(code)```, so that dashboard component can later decide what to do with the accessToken.
- Update the "test" key value in scripts in the package.json to "devStart": "nodemon server.js".
  - Node is a runtime environment for running JavaScript code. server.js is usually the main entry file of a Node.js application, which contains the code related to the server. Running the command "node server.js" is to start this Node.js server application.
- Debug: We then immediately see a CORS error in the browser console. But it is very simple, we just run ```npm i cors``` in server dir. And then import this package in server.js. ```const cors = require('cors');
``` and ```app.use(cors());```
  - CORS (Cross-Origin Resource Sharing) is a mechanism between browsers and servers, which is used to control resource requests between different origins. A front-end application (most likely running at http://localhost:3000) initiates a request to the server (running at http://localhost:3001). Since these two addresses belong to different origins, the browser blocks the request according to the same-origin policy. Therefore, we install "cors". "Cors" is a Node.js middleware used to configure CORS rules on the server side. The server can then tell the browser which origins are allowed to access its resources, and which request methods (such as GET, POST, etc.) and request headers are acceptable.
- Debug: the error msg is then changed to 500, which means our ```req.body``` cannot be read(parsed), so we need to ```npm i body-parser```. And again update the server.js ```const bodyParser = require(“body-parser”)``` and ```app.use(bodyParser.json())``` to use json body parser
- Debug: Next, we faced a common issue request failed 400, but no error on our server. If we log the error, it says: Invalid_grant Authorization code expired.. This is because we can only use the access code once, we need a new code
  ```.catch((err) => {
          console.log(err);
          res.sendStatus(400);
      })
  ```
- So we add a ```.catch(()=>{window.location = '/'})``` in the useEffect of useAuth, that redirect the window location(web page) back to the root, which is the user login page.
- Also, we don’t want the token to be exposed in the URL, so we add ```window.history.pushState({}, null, '/')``` in useEffect of useAuthjs.
  - history.pushState(state, title, url) is used to add a new record to the browser's history and can also modify the URL in the current browser address bar.
    - state: {} is an empty state object that doesn't store additional information. This is a state object. When the user browses the history using the browser's forward or backward buttons, this state object will be passed to the popstate event handler.
    - title: null means the page title is ignored. Currently, most browsers will ignore this parameter, so it's common to pass null.
    - url: "/" indicates that the URL in the browser address bar is modified to the root path. It's the URL corresponding to the new history record and can be any valid URL within the same origin as the current page.
- We return the access token in useAuth, which is essential to call all different spotify apis. But it only last for an hour
```
    useEffect(() => {
        axios.post('http://localhost:3001/login',{
            code,
        }).then(res => {
            setAccessToken(res.data.accessToken)
            setRefreshToken(res.data.refreshToken)
            setExpiresIn(res.dataßß.expiresIn)
            window.history.pushState({}, null, '/')
        }).catch(()=>{
            window.location = '/'
        })
    }, [code])
    return accessToken
```

