# How to implement a Mock Server in Android using Json Server?

I recently had a requirement to create a mock server for testing purposes in Android. This means we would have to setup a mock server in our system which would simulate the backend response. A great advantage of this is that the app can be tested without any dependency on the backend. </br>

We can implement a mock server using [JsonServer](https://github.com/typicode/json-server). Let's see how to implement this.
  * <b>Step 1</b>: Install JSON Server:
    ```
    npm install -g json-server
    ```
    Note: In order to run the ```npm``` command, we need to install ```node.js``` first. Use this [link](https://www.npmjs.com/get-npm) to download and install it.
  
  * <b>Step 2</b>: Create a ```db.json``` file in the root folder of your app project. This file will contain all the mock responses we need to simulate from the server. Let's say you have a login screen. And the login response is something like this:
    ```
    {
        "status": 2,
        "message": "Login is successful"
    }
    ```
    Then we would need to add a mock response in the ```db.json``` file like this:
    ```
    {
      "login": {
          "status" : 1,
          "message": "User logged in successfully"
      }
    }
    ```
    We need to give a name to every request data we add. <b>***This is very important***</b>. We need to add  For instance, if we need to add data for logout, then the ```db.json``` will look like this:
    ```
    {
      "login": {
          "status" : 1,
          "message": "User logged in successfully"
      },
      "logout" : {
        "status" : 1,
        message: "User logged out"
      }
    }
    ```
    
    
  * <b>Step 3</b>: Create a ```routes.json``` file in the root folder of your app project. This file will map your custom path urls to the corresponding mock response in the ```db.json```. So for the above login and logout response, the ```routes.json``` will be:
    ```
    {
      "/api/logout": "/logout",    // -> Change the "api/logout" to your relevant path url. 
      "/api/login\\?userId=:userId": "/login?userId=:userId" // -> Change the "api/login" to your relevant path url. If we need to pass a query parameter to the request, then we can do it like this.
    }
    ```
    You can create a whole bunch of routes for your servers, like adding path parameters, query parameters, request body etc. Checkout the entire list from [here](https://github.com/typicode/json-server#add-custom-routes).
    
  * <b>Step 4</b>: Create a ```middleware.js``` file in the root folder of your app project. An example would be:
    ```
    module.exports = function (req, res, next) {
      if (req.method === 'POST') {
        // Converts POST to GET and move payload to query params
        // This way it will make JSON Server that it's GET request
        req.method = 'GET'
        req.query = req.body
      }
      // Continue to JSON Server router
      next();
    }
    ```
    
   * <b>Step 5</b>: Open the terminal inside your Android Studio and run the below command to start your local server:
    ```
    json-server --watch db.json --port 3004 --routes routes.json --middlewares middleware.js
    ```
    
   * <b>Step 6</b>: Change the base url of your server in the app. I generally use ```ngrok``` to get a mock url of the local server. You can use this [guide](https://ngrok.com/) to get started with ```ngrok```.
  
  
  
  
  
  
  
  
  
