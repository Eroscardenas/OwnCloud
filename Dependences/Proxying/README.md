# Welcome to the area Cloud Computing wurh docker :whale:
![ReferenceImage](/images/📡 Proxy🐋.png)

## *We  recommended use Nginx*
![ReferenceImage, width: 15](/images/gigi.webp)

***nginx-proxy*** sets up a container running in nginx and *docker-hen*. Docker-gen generates preverse **proxy** configs for **nginx** and reloads nginx when containers are started and stopped.

- **Dockerfile**: Defines the Docker image based on `nginx:latest`.
- **nginx.conf**: Custom Nginx configuration for serving static files, handling errors, and applying optimizations.
- **index.html**: The default static webpage served by Nginx.

## Dockerfile

```dockerfile
FROM nginx:latest

# Copy custom configuration files
COPY nginx-config/nginx.conf /etc/nginx/nginx.conf
COPY nginx-html/ /usr/share/nginx/html/
```
This ``Dockerfile`` pulls the latest Nginx image from Docker Hub, copies your custom configuration (*nginx.conf*) to ``/etc/nginx/``, and serves static HTML files from ``/usr/share/nginx/html.``

#### [DockerHub-Image](https://hub.docker.com/_/nginx?uuid=9E4A6F83-9251-4C93-B16E-CF90CF11B843)


## *nginx.conf Configuration*
```events {
    worker_connections 1024;
    use epoll;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    client_max_body_size 16M;

    gzip on;
    gzip_types text/css application/javascript text/html application/xml;
    gzip_min_length 256;

    server {
        listen 80;
        server_name localhost;

        root /usr/share/nginx/html;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
            expires 30d;
            access_log off;
        }

        error_page 404 /404.html;
        location = /404.html {
            root /usr/share/nginx/html;
            internal;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /usr/share/nginx/html;
            internal;
        }
    }
}
```
# Usage
In Docker Hub we will search "NGINX" and the first option with 1B pulls,  located in the Folder when we re gonna created put 

`docker pull nginx:stable-alpine3.20-perl ` or `docker pull nginx:latest ` 

Then we're gonna created another folder where we're gonna configure our nginx, inside we will create a file *index.html*


### Volume
Volumes in Docker are used to persist data beyond the lifecycle of a container. In the case of Nginx, you might want to use volumes to store configuration files, SSL certificates, logs, or any other resources that Nginx needs to access.

`docker run -d -v /OwnCloud/docker1/nginx.conf:/etc/nginx/nginx.conf -p 80:80 index.html
`
## Ports
8080:80

## Key Sections
- **events block**: Configures the maximum number of simultaneous connections (*`worker_connections`*) and optimizes connection handling using *`epoll`*.
- **http block:**
gzip compression: Improves performance by compressing file types like CSS, JavaScript, HTML, and XML.
server block: Handles all requests on port 80 and serves static files from ``/usr/share/nginx/html/``. It also defines custom error pages for 404 and 500-series errors.

### Custom Error Pages

- *404 Page*: Custom page located at /404.html to handle "Not Found" errors.
- *50x Pages*:  Custom error pages for server-side errors such as 500, 502, 503, and 504.

## Steps to Build and Run the Project

#### Build the Docker image:
`docker-compose up --build`

#### Verify the container is running:
`docker ps`

Ensure that your Nginx container is running and accessible.


*Access the project:* Open a browser and go to `http://localhost` to see the default `index.html.`

## Troubleshooting

- ***MIME type warning:*** Ensure you don’t duplicate MIME types by including both `include /etc/nginx/mime.types;` and `default_type` settings without redundancies.
- ***Error 404 or 50x:*** If you encounter custom error pages, ensure that the corresponding HTML files `(404.html, 50x.html)` are placed correctly in the root directory.

### Future Improvements

- ***SSL/TLS Support:*** Add SSL certificates for secure HTTPS connections.
- ***Proxying to a Backend:*** Expand the project to reverse proxy requests to a backend server (Node.js, Python, etc.).
- ***Monitoring:*** Integrate tools like Prometheus and Grafana for real-time monitoring of your Nginx server performance.