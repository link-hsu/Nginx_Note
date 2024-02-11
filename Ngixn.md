# Nginx Course
- [Course-Link](https://www.youtube.com/watch?v=7VAI73roXaY "nginx")
- Nginx is a server for special purpose
- Install nginx
  ~~~
  brew install nginx
  
  ls
  code .
  
  nginx -t
  nginx: the configuration file /opt/homebrew/etc/nginx/nginx.conf

  nginx
  nginx -s reload
  ~~~
- directive and context
  ~~~
  events {
    worker_connections  1024;
  }
  * worker_connections  1024 <- directive
  * events {  } <- context
  ~~~
  ~~~
  // nginx.conf
  http {
    // types {
    //   text/css        css;
    //   text/html       html;
    // }
    include mine.types;
    server {
      listen 8080;
      root /Users/laithharb/Desktop/mysite;
    }
  }
  events {

  }
  ~~~
- Location Block
  ~~~
  // nginx.conf
  http {
    // types {
    //   text/css        css;
    //   text/html       html;
    // }
    include mine.types;
    server {
      listen 8080;
      root /Users/laithharb/Desktop/mysite;

      // 在 nginx 配置中，~* 是一個正則表達式匹配符，它表示後面跟隨的字符串是一個不區分大小寫的正則表達式。
      location ~* /count/[0-9] {
        root /Users/laithharb/Desktop/mysite;
        try_files /index.html =404;
      }

      location /fruits {
        // /fruits wil append - mysite/fruits
        root /Users/laithharb/Desktop/mysite;
      }

      location /carbs {
        // /carbs will not append with alias - 
        // mysite/fruits
        alias /Users/laithharb/Desktop/mysite/fruits;
      }

      location /vegetables {
        root /Users/laithharb/Desktop/mysite;
        try_files /vegetables/veggies.html /index.html =404; // 依序嘗試查找path
      }
    }
  }
  events {

  }
  ~~~
- Redirect and Rewrite
  ~~~
  // nginx.conf
  http {
    // types {
    //   text/css        css;
    //   text/html       html;
    // }
    include mine.types;
    server {
      listen 8080;
      root /Users/laithharb/Desktop/mysite;
      ...
      // ^ 表示正則表達式的開始。
      rewrite ^/number/(\w+) /count/$1;

      ...
      location /crops {
        return 307 /fruits;
      }
    }
  }
  events {

  }  
  ~~~
- Load Balancer
  - use Docker to build multi servers
    1. ```mkdir server```  
       ```git init```  
       ```npm install express```  
       ```// create expess server```  
       
       ~~~
       # Dockerfile
       FROM node:18.16.0-alpine3.17  
       RUN mkdir -p /opt/app  
       WORKDIR /opt/app  
       COPY src/package.json src/package-lock.   json .  
       RUN npm install  
       COPY src/ .  
       EXPOSE 3000  
       CMD [ "npm", "start"] 
       ~~~ 
       ```docker build . -t myserver```  
       ```docker run -p 1111:7777 -d myserver```  
       ```docker run -p 2222:7777 -d myserver```  
       ```docker run -p 3333:7777 -d myserver```  
       ```docker run -p 4444:7777 -d myserver```
  - ~~~
    http {
      include mime.types;
      upstream backendserver { 
        server 127.0.0.1:1111;
        server 127.0.0.1:2222;
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
      }
      server {
        listen 8080;
        root ...
        location / {
          proxy_pass http://backendserver/;
        }
      }
    }
    ~~~

       
  