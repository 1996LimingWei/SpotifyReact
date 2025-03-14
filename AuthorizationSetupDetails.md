# Authorization Setup Details

- we create Login.js in client/src. We can use rfc to generate the general functional component. And we can initialize the ```const AUTH_URL = "https://accounts.spotify.com/authorize"``` and add all the query paramenter into the AUTH_URL, follow them with a “=” and separate by “&”.
- we use bootstrap for styling in the login.js and app.js, install the package by running
```cd client/```,
```npm i boostrap react-bootstrap```, and
```npm install bootstrap```
- To ensure we have the styles being render, we need to add minified boostrap CSS```import 'import 'bootstrap/dist/css/bootstrap.min.css';``` in App.js
  - The app.js file is often the root component of a React application. When we import the minified Bootstrap CSS in app.js, it makes the Bootstrap styles available globally.
- We can remove App.css, App.test.js, index.css, and logo.svg from src dir. We just need a bare-bone application
- In Login.js, initialize the bootstrap container by adding it to the export default function login’s return method
  - The container class in Bootstrap is a fundamental part of its responsive grid system(rows and columns, gutter). It provides a centered and responsive wrapper for your content. It helps in organizing the content within the login.js component.
- After solving conflicts from removing unnecessary files, the browser window should look like this: 
	![截屏2025-03-14 15 37 22](https://github.com/user-attachments/assets/0473497c-83a9-4ce9-b4ef-a546af15b7d5)
