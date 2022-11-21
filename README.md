OAuth 2 Study Project
=====================

A set of start-from-scratch OAuth applications in JavaScript using the [Express.js](http://expressjs.com/) web
application framework running on [Node.js](https://nodejs.org/), a server-side JavaScript engine.

We are only making use of library code for non-OAuth-specific functionality to avoid complicated dependencies

Each component is set up to run on a different port on localhost, in a separate process:

- The OAuth Client application (client.js) runs on http://localhost:9000/

  ![Error loading client-js.png](./client-js.png)

- The OAuth Authorization Server application (authorizationServer.js) runs on http://localhost:9001/

  ![Error loading client-js.png](./authorizationServer-js.png)

- The OAuth Protected Resource Application (protectedResource.js) runs on http://localhost:9002/

  ![Error loading client-js.png](./protectedResource-js.png)

> - protected resource and authorization server share a [file-based NoSQL db](https://www.npmjs.com/package/nosql)
>   located in the same directory. The file name is "database.nosql". Note that editing this file by hand is dangerous
>   while the system is running. Luckily, resetting the database is as simple as deleting the "database.nosql" file and
>   restarting the programs. Note that this file isn't created until the authorization server stores a token in it the
>   first time, and its contents are reset every time the authorization server is restarted.

1. [Basic Client + Auth Server + Resource Server](./1-basic)
2. [Refresh access token](./2-refresh-accessd-token)
3. [Parsing token in resource server](./3-parsing-token-in-resource-server)
4. [Different kinds of HTTP methods require different scopes in order for the call to be successful](./4-scopped-http-methods)
5. [Different data results for different scopes](./5-different-scopes-for-different-data-results)
6. [Different data results for different users](./6-different-data-results-for-different-users)

To run each module, `cd` into that module and start all components by

```bash
npm install
node client.js & node authorizationServer.js & node protectedResource.js
```

To stop all components:

```bash
ps -a | grep -E -- 'client.js|authorizationServer.js|protectedResource.js'| awk '{print $1}' | xargs kill
```

Notes
-----

All of the applications have been set up to serve static files such as images and Cascading Style Sheets (CSS). These
are included in the `files` directory. In addition, there are HTML templates in the `files` directory. These are used in 
the applications to generate HTML pages based on variable inputs. When templates are used, they are set up at the
beginning of the application with the following code:

```javascript
app.engine('html', cons.underscore);
app.set('view engine', 'html');
app.set('views', 'files');
```


License
-------

The use and distribution terms for [oauth-in-action-code](https://qubitpi.github.io/oauth-in-action-code/) are covered
by the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).

<div align="center">
    <a href="https://opensource.org/licenses">
        <img align="center" width="50%" alt="License Illustration" src="https://github.com/QubitPi/QubitPi/blob/master/img/apache-2.png?raw=true">
    </a>
</div>
