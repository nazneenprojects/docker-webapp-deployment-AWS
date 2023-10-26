### Use Case: Create and Deply Simple NodeJS App on docker based EC2 instance
### Steps:
1. Create a Node JS sample web app locally  (Refer code files in this repo)
2. Add Dockerfile to create image (Refer file in this repo)
3. Run docker build in local env  -> docker build -t front-end-app-1 
4. Run docker run in local env
5. Check if app is running locally on browser port
6. Push this image (app with required enviorment set up) to Docker Hub Repository "namu123/front-end-app-1:demo"
7. Launch Simple AWS EC2 instance for hosting a web app (with default VPC, subnet. Only add new rules for security group inbound traffic for SSH and HTTP access)
8. Install Docker on EC2 instance
9. Pull this image onto EC2 instance using docker run command
10. Pick up public IP of EC2 Instance and hit into the browser.
11. Web is hosted onto AWS Cloud

### run app locally 

```
zermatt@zermatt:~/Documents/Docker$ cd front-end-app-1/

zermatt@zermatt:~/Documents/Docker/front-end-app-1$ sudo docker build -t front-end-app-1 .
[sudo] password for zermatt: 
[+] Building 7.2s (10/10) FINISHED                                                                               docker:default
 => [internal] load build definition from Dockerfile                                                                       0.8s
 => => transferring dockerfile: 153B                                                                                       0.0s
 => [internal] load .dockerignore                                                                                          1.3s
 => => transferring context: 68B                                                                                           0.0s
 => [internal] load metadata for docker.io/library/node:14-alpine                                                          1.2s
 => [1/5] FROM docker.io/library/node:14-alpine@sha256:434215b487a329c9e867202ff89e704d3a75e554822e07f3e0c0f9e606121b33    0.0s
 => [internal] load build context                                                                                          0.5s
 => => transferring context: 7.68kB                                                                                        0.0s
 => CACHED [2/5] WORKDIR /app                                                                                              0.0s
 => CACHED [3/5] COPY package.json .                                                                                       0.0s
 => CACHED [4/5] RUN npm install                                                                                           0.0s
 => [5/5] COPY . .                                                                                                         2.2s
 => exporting to image                                                                                                     0.8s
 => => exporting layers                                                                                                    0.5s
 => => writing image sha256:8fe44764bf632e9ab6148c4e3db7555e68a05b09071479019493964b3fda07d8                               0.1s
 => => naming to docker.io/library/front-end-app-1                                                                         0.2s

zermatt@zermatt:~/Documents/Docker/front-end-app-1$ sudo docker tag front-end-app-1 namu123/front-end-app-1:demo

zermatt@zermatt:~/Documents/Docker/front-end-app-1$ sudo docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED          STATUS          PORTS                               NAMES
9981b0aefa85   node-dep-example   "docker-entrypoint.s…"   49 minutes ago   Up 49 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   node-dep

zermatt@zermatt:~/Documents/Docker/front-end-app-1$ sudo docker images
REPOSITORY                TAG       IMAGE ID       CREATED              SIZE
front-end-app-1           latest    8fe44764bf63   About a minute ago   124MB
namu123/front-end-app-1   demo      8fe44764bf63   About a minute ago   124MB
node-dep-example          latest    82b4f5ba3016   50 minutes ago       124MB
<none>                    <none>    ab056228d310   2 hours ago          916MB
hello-world               latest    9c7a54a9a43c   5 months ago         13.3kB
zermatt@zermatt:~/Documents/Docker/front-end-app-1$ sudo docker login
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: namu123
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

zermatt@zermatt:~/Documents/Docker/front-end-app-1$ sudo docker push namu123/front-end-app-1:demo
The push refers to repository [docker.io/namu123/front-end-app-1]
416a7e68ef0d: Pushed 
47c4e603eed0: Pushed 
d25ac171367d: Pushed 
5f6c51a04c2b: Pushed 
31f710dc178f: Mounted from library/node 
a599bf3e59b8: Mounted from library/node 
e67e8085abae: Mounted from library/node 
f1417ff83b31: Mounted from library/node 
demo: digest: sha256:ba330b6ba41d4a0138735cec5b53ebe9add7ad21e2a23c999b1536821c97530a size: 1990

```
