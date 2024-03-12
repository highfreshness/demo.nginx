# Nginx Demo
## Goals
   When you send an HTTP request with an access token to `Nginx/login` location, FastAPI validates the token and redirects you to a private page or prints a message with a 401 response.

## Tech stack
Docker(4.27.2)  
Docker compose(v2.24.5-desktop.1)  
Nginx(1.24.0)  
FastAPI(0.110.0)  
Ubuntu(20.04)  
Python(3.10.13)  


## Pre-requisite installations
Docker  
Postman(or curl)   

## Install and run
1. `git clone https://github.com/highfreshness/demo.nginx.git . && cd demo.nginx` 
2. `docker compose up -d` or     
`docker compose up -d && docker compose logs -f`(optional - with log)
3. Connect to `localhost:8000/docs`
4. You need to enter your ID/PW on the /token endpoint and receive an access token. ( ID:johndoe, PW:secret )
5. The obtained token is used in postman or curl to send a request to `localhost/login` by adding the value to the authorization header.  
   (example) `curl -L http://localhost/login -H 'Authorization: Bearer <ACCESS TOKEN>`
6. If the token in the sent request is valid, you will receive the "message": "Successful authentication!" message, otherwise you will receive a "detail": "Could not validate credentials" message will be received.

## 3. Feature descriptions
### Nginx
Nginx.conf
```shell
pid /var/run/nginx.pid; 

events {}

http {

    upstream api-server {
        server fastapi:8000;
    }

    include /etc/nginx/mime.types;

    server {
        listen 80;
        location /login {
            auth_request /auth; 
            error_page 401 = @auth_failed;
            rewrite / /auth/success break;
            proxy_pass http://api-server;
        }

        location /auth {
            internal;
            rewrite / /users/me break;
            proxy_pass http://api-server;
        }

        location /proxy_test {
            rewrite / /check break;
            proxy_pass http://api-server;
        }
  
        location @auth_failed {
            internal;
            rewrite / /users/me break; 
            proxy_pass http://api-server;
        }
    }
}
```

### nginx.conf options
**auth_request** : Implement client authentication based on the result of the subrequest. If the sub-request returns a 2xx response code, access is granted; otherwise, access is denied. (The response does not contain an HTTP body.)

**error_page** : Defines the URI that will be shown for the specified errors. A uri value can contain variables.

**rewrite** : If the specified regular expression matches the request URI, the URI is changed as specified in the substitution string. 

**internal** : Specifies that a given location can only be used for internal requests. For external requests, the client error 404 (Not Found) is returned.


### Logic on Auth_request failure
auth_request does not include the HTTP body when it receives a response, but if it receives a 401 response, it is redirected to @auth_failed, which signals the FastAPI's auth authentication endpoint and allows it to represent the body of the HTTP response.


## Reference
Nginx documentation : https://nginx.org/en/docs/  
ChatGPT   
Stackoverflow


