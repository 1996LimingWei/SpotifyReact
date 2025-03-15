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
  - Axios is a Promise - based HTTP client that can be used in both browser and Node.js environments. It is mainly used to send HTTP requests to servers, such as GET, POST, PUT, DELETE, etc. Compared with the native fetch API, it has the following advantages:
    - Concise and easy: It provides a more concise and intuitive API, increase readability.
    - Interceptors: It can intercept and process requests before they are sent and responses after they are returned, which is convenient for global request and response processing, such as adding request headers and handling errors.
    - Promise support: It returns a Promise object, which facilitates the use of then and catch for chained calls to handle asynchronous operations.
      - Asynchronous operations are tasks that don't block the execution of subsequent code in a program. They let a program carry on with other code while a time-consuming task, like a network request (e.g., with Axios), file reading, or a timer operation (using setTimeout), is in progress.
- If we go back to the server.js, we see that we are not running our application on any port. What does it mean?
  - A port is a number-based identifier that is used to distinguish different applications running on a computer. It's like a specific "door" through which data can flow in and out of a particular process. Each port number is unique within a given system.
  - When a server application "listens" to a port, it means that the application is configured to wait for incoming network connections on that specific port. Like the server is "sitting by the door" and waiting for clients to send requests
- We need also add app.listen(3001) for now. With this code, we post our code to this 3001 route. After post the code, we received either pending, fulfilled, or reject. And then we use .then to log what is returned 

•	Update nodemon as we are going to run the server. node 是运行 JavaScript 代码的运行环境。server.js 通常是一个 Node.js 应用程序的主入口文件，里面包含了服务器相关的代码。运行 node server.js 命令就是启动这个 Node.js 服务器应用。 nodemon 前面提到过，会监视项目中的文件变化，一旦检测到文件被修改，它会自动重启 Node.js 应用，无需手动操作。 
•	next, we make sure we pull in the useAuth to the dashboard.js, so that dashboard component can later decide what to do with the accessToken.  
•	Debug: We then immediately see this in the console of the web page. We just need to install npm i cors and import it to the server.js:  
a)	const cors = require(“cors”)
b)	app.use(cors())

•	CORS（Cross-Origin Resource Sharing，跨域资源共享）是一种浏览器和服务器之间的机制，用于控制不同源（origin）之间的资源请求。前端应用（很可能运行在 http://localhost:3000 ）向服务器（运行在 http://localhost:3001 ）发起了请求，由于这两个地址属于不同的源，浏览器根据同源策略阻止了该请求。所以我们安装cors，cors 是一个 Node.js 中间件，用于在服务器端配置 CORS 规则。服务器就可以告诉浏览器哪些源被允许访问它的资源，哪些请求方法（如 GET、POST 等）和请求头是被接受的。
•	Debug: the error msg is then changed to 500, which is our code cannot be read(parsed), so we need to npm i body-parser. And again update the server.js: 
a)	const bodyParser = require(“body-parser”)
b)	app.use(bodyParser.json())
•	Next, we faced a crucial issue. If we catch the spotifyApi function and log the error. It says:  This is because we can only use the access code once
•	So we add a catch in the useEffect of useAuth, that redirect the window location(web page) back to the root, which is the user login page. Also, we don’t want the token to be exposed in the URL, so we use history.pushState(state, title, url) 用于在浏览器的历史记录中添加一条新记录，同时可以修改当前浏览器地址栏的 URL 
a)	state： {} 是一个空的状态对象，没有存储额外信息。这个是一个状态对象，当用户通过浏览器的前进或后退按钮浏览历史记录时，这个状态对象会被传递给 popstate 事件处理函数。
b)	title：null 表示忽略页面标题。目前大多数浏览器会忽略这个参数，一般传入 null 即可。
c)	url："/" 表示将浏览器地址栏的 URL 修改为根路径。新的历史记录对应的 URL ，可以是与当前页面同域的任意有效 URL 。
![image](https://github.com/user-attachments/assets/fdbccaf2-73a5-4280-aa3c-e937c13c9c38)
