# Tomcat CSRF Filter

This project contains the Tomcat `RestCsrfPreventionFilter` copied from Tomcat's own repo. This is here to support projects that need CSRF protection but are not using Tomcat, since this filter is one of the best CSRF prevention tools out there.

All credit for the code goes to Apache Tomcat.

## Servlet Version

For maximum compatibility, this library currently uses `javax.servlet` instead of `jakarta.servlet`. That's the only change made to the Tomcat source code.

It is expected that consuming projects will have this dependency, either directly or from an implementation library:

```
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>${javax.servlet.version}</version>
</dependency>
```

## Adding the Filter

The `RestCsrfPreventionFilter` needs to be manually added to the servlet it needs to filter. How this is done will vary based on the implementation, be it Tomcat, Spring Embedded Tomcat, Jetty, etc.

## Using CSRF

This library will enforce CSRF protection across the application. This means all modifying calls (GET, POST, PUT, etc) will require a CSRF synchronizer token to be provided. Here is how to work with this:

### Step 1 - Get the CSRF Token

The current, valid CSRF token for the user session can be acquired at any time by making an HTTP GET request with the CSRF Fetch header. The token will be returned in the CSRF response header, and can then be stored. The token will be returned on both a success and failed call.

When the user first reaches the site, be sure to make an HTTP GET request like demonstrated below and store the CSRF token in memory. It should be stored in memory, and not `localStorage` or `sessionStorage` for maximum security.

```
axios.get('/oauth/user', {
    headers: {
        'x-csrf-token': 'fetch'
    }
})
    .then((res) => {
        // CSRF token is at res.headers['x-csrf-token']
    })
    .catch((ex) => {
        // CSRF token is at ex.response.headers['x-csrf-token']
    });
```

### Step 2 - Send the CSRF Token in All Modifying Requests

After this, all modifying requests need to provide the CSRF token in the CSRF header. This can be easily done using an `axios` interceptor:

```
export const addCsrfTokenInterceptor = (config) => {
    const csrfToken = getCsrfTokenFromMemory();
    if (csrfToken && config.method !== 'get') {
        config.headers = {
            ...config.headers,
            ['x-csrf-token']: csrfToken
        };
    }
    return config;
};

axios.interceptors.request.use(addCsrfTokenInterceptor)
```