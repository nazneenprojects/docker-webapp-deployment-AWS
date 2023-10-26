### Use Case: Create and Depoly Simple NodeJS App on docker based EC2 instance
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

### run app locally and push to Docker Hub Repository

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


### Hosting web app on AWS EC2 with docker

```
zermatt@zermatt:~/Documents/Docker$ cd deployment-01-starting-setup/
zermatt@zermatt:~/Documents/Docker/deployment-01-starting-setup$ chmod 400 ec2-dock-frankfurt.pem 
zermatt@zermatt:~/Documents/Docker/deployment-01-starting-setup$ ssh -i "ec2-dock-frankfurt.pem" ec2-user@ec2-3-71-206-26.eu-central-1.compute.amazonaws.com
The authenticity of host 'ec2-3-71-206-26.eu-central-1.compute.amazonaws.com (3.71.206.26)' can't be established.
ED25519 key fingerprint is SHA256:BbOctJsJyC98hMt1ElaLqZq6ZU5NYJo0uWr5zm4gEHU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-3-71-206-26.eu-central-1.compute.amazonaws.com' (ED25519) to the list of known hosts.
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
[ec2-user@ip-172-31-27-228 ~]$ sudo yum update -y
Last metadata expiration check: 0:03:17 ago on Thu Oct 26 10:04:39 2023.
Dependencies resolved.
Nothing to do.
Complete!
[ec2-user@ip-172-31-27-228 ~]$ sudo amazon-linux-extras install docker
sudo: amazon-linux-extras: command not found
[ec2-user@ip-172-31-27-228 ~]$ sudo yum -y install docker
Last metadata expiration check: 0:04:25 ago on Thu Oct 26 10:04:39 2023.
Dependencies resolved.
===================================================================================
 Package                   Arch      Version                  Repository      Size
===================================================================================
Installing:
 docker                    x86_64    24.0.5-1.amzn2023.0.2    amazonlinux     42 M
Installing dependencies:
 containerd                x86_64    1.7.2-1.amzn2023.0.4     amazonlinux     34 M
 iptables-libs             x86_64    1.8.8-3.amzn2023.0.2     amazonlinux    401 k
 iptables-nft              x86_64    1.8.8-3.amzn2023.0.2     amazonlinux    183 k
 libcgroup                 x86_64    3.0-1.amzn2023.0.1       amazonlinux     75 k
 libnetfilter_conntrack    x86_64    1.0.8-2.amzn2023.0.2     amazonlinux     58 k
 libnfnetlink              x86_64    1.0.1-19.amzn2023.0.2    amazonlinux     30 k
 libnftnl                  x86_64    1.2.2-2.amzn2023.0.2     amazonlinux     84 k
 pigz                      x86_64    2.5-1.amzn2023.0.3       amazonlinux     83 k
 runc                      x86_64    1.1.7-1.amzn2023.0.3     amazonlinux    3.0 M

Transaction Summary
===================================================================================
Install  10 Packages

Total download size: 80 M
Installed size: 306 M
Downloading Packages:
(1/10): libnftnl-1.2.2-2.amzn2023.0.2.x86_64.rpm   1.0 MB/s |  84 kB     00:00    
(2/10): iptables-libs-1.8.8-3.amzn2023.0.2.x86_64. 4.2 MB/s | 401 kB     00:00    
(3/10): pigz-2.5-1.amzn2023.0.3.x86_64.rpm         4.7 MB/s |  83 kB     00:00    
(4/10): libnfnetlink-1.0.1-19.amzn2023.0.2.x86_64. 2.0 MB/s |  30 kB     00:00    
(5/10): runc-1.1.7-1.amzn2023.0.3.x86_64.rpm        21 MB/s | 3.0 MB     00:00    
(6/10): libcgroup-3.0-1.amzn2023.0.1.x86_64.rpm    1.6 MB/s |  75 kB     00:00    
(7/10): iptables-nft-1.8.8-3.amzn2023.0.2.x86_64.r 7.1 MB/s | 183 kB     00:00    
(8/10): libnetfilter_conntrack-1.0.8-2.amzn2023.0. 2.4 MB/s |  58 kB     00:00    
(9/10): containerd-1.7.2-1.amzn2023.0.4.x86_64.rpm  46 MB/s |  34 MB     00:00    
(10/10): docker-24.0.5-1.amzn2023.0.2.x86_64.rpm    39 MB/s |  42 MB     00:01    
-----------------------------------------------------------------------------------
Total                                               64 MB/s |  80 MB     00:01     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                           1/1 
  Installing       : runc-1.1.7-1.amzn2023.0.3.x86_64                         1/10 
  Installing       : containerd-1.7.2-1.amzn2023.0.4.x86_64                   2/10 
  Running scriptlet: containerd-1.7.2-1.amzn2023.0.4.x86_64                   2/10 
  Installing       : libcgroup-3.0-1.amzn2023.0.1.x86_64                      3/10 
  Installing       : libnfnetlink-1.0.1-19.amzn2023.0.2.x86_64                4/10 
  Installing       : libnetfilter_conntrack-1.0.8-2.amzn2023.0.2.x86_64       5/10 
  Installing       : iptables-libs-1.8.8-3.amzn2023.0.2.x86_64                6/10 
  Installing       : pigz-2.5-1.amzn2023.0.3.x86_64                           7/10 
  Installing       : libnftnl-1.2.2-2.amzn2023.0.2.x86_64                     8/10 
  Installing       : iptables-nft-1.8.8-3.amzn2023.0.2.x86_64                 9/10 
  Running scriptlet: iptables-nft-1.8.8-3.amzn2023.0.2.x86_64                 9/10 
  Running scriptlet: docker-24.0.5-1.amzn2023.0.2.x86_64                     10/10 
  Installing       : docker-24.0.5-1.amzn2023.0.2.x86_64                     10/10 
  Running scriptlet: docker-24.0.5-1.amzn2023.0.2.x86_64                     10/10 
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /usr/lib/systemd/system/docker.socket.

  Verifying        : runc-1.1.7-1.amzn2023.0.3.x86_64                         1/10 
  Verifying        : libnftnl-1.2.2-2.amzn2023.0.2.x86_64                     2/10 
  Verifying        : iptables-libs-1.8.8-3.amzn2023.0.2.x86_64                3/10 
  Verifying        : pigz-2.5-1.amzn2023.0.3.x86_64                           4/10 
  Verifying        : libnfnetlink-1.0.1-19.amzn2023.0.2.x86_64                5/10 
  Verifying        : libcgroup-3.0-1.amzn2023.0.1.x86_64                      6/10 
  Verifying        : docker-24.0.5-1.amzn2023.0.2.x86_64                      7/10 
  Verifying        : iptables-nft-1.8.8-3.amzn2023.0.2.x86_64                 8/10 
  Verifying        : containerd-1.7.2-1.amzn2023.0.4.x86_64                   9/10 
  Verifying        : libnetfilter_conntrack-1.0.8-2.amzn2023.0.2.x86_64      10/10 

Installed:
  containerd-1.7.2-1.amzn2023.0.4.x86_64                                           
  docker-24.0.5-1.amzn2023.0.2.x86_64                                              
  iptables-libs-1.8.8-3.amzn2023.0.2.x86_64                                        
  iptables-nft-1.8.8-3.amzn2023.0.2.x86_64                                         
  libcgroup-3.0-1.amzn2023.0.1.x86_64                                              
  libnetfilter_conntrack-1.0.8-2.amzn2023.0.2.x86_64                               
  libnfnetlink-1.0.1-19.amzn2023.0.2.x86_64                                        
  libnftnl-1.2.2-2.amzn2023.0.2.x86_64                                             
  pigz-2.5-1.amzn2023.0.3.x86_64                                                   
  runc-1.1.7-1.amzn2023.0.3.x86_64                                                 

Complete!
[ec2-user@ip-172-31-27-228 ~]$ sudo service docker start
Redirecting to /bin/systemctl start docker.service
[ec2-user@ip-172-31-27-228 ~]$ sudo usermod -a -G docker ec2-user
[ec2-user@ip-172-31-27-228 ~]$ exit
logout
Connection to ec2-3-71-206-26.eu-central-1.compute.amazonaws.com closed.
zermatt@zermatt:~/Documents/Docker/deployment-01-starting-setup$ ssh -i "ec2-dock-frankfurt.pem" ec2-user@ec2-3-71-206-26.eu-central-1.compute.amazonaws.com
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Thu Oct 26 10:06:52 2023 from 149.233.185.174
[ec2-user@ip-172-31-27-228 ~]$ sudo systemctl enable docker
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /usr/lib/systemd/system/docker.service.
[ec2-user@ip-172-31-27-228 ~]$ docker version
Client:
 Version:           24.0.5
 API version:       1.43
 Go version:        go1.20.10
 Git commit:        ced0996
 Built:             Fri Oct 13 00:00:00 2023
 OS/Arch:           linux/amd64
 Context:           default

Server:
 Engine:
  Version:          24.0.5
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.10
  Git commit:       a61e2b4
  Built:            Fri Oct 13 00:00:00 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.2
  GitCommit:        0cae528dd6cb557f7201036e9f43420650207b58
 runc:
  Version:          1.1.7
  GitCommit:        f19387a6bec4944c770f7668ab51c4348d9c2f38
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
[ec2-user@ip-172-31-27-228 ~]$ docker run -d --rm -p 80:80 namu123/front-end-app-1:demo
Unable to find image 'namu123/front-end-app-1:demo  locally
latest: Pulling from academind/node-example-1
cbdbe7a5bc2a: Pull complete 
d22524cae38d: Pull complete 
a31d6a083b03: Pull complete 
38017abc27d5: Pull complete 
8af7f4c6da68: Pull complete 
f9ead9b39b57: Pull complete 
f93e345d8813: Pull complete 
d7b22ae04ebb: Pull complete 
Digest: sha256:e3a3157928f7c24e0c88391bc79901d5c7264c860b52fdde879aa44595d89d7b
Status: Downloaded newer image for namu123/front-end-app-1:demo
3bb32075268e4518c49a35c5aae03fe7adeb8aea88f5929f9017e0b6b6c4c23f
[ec2-user@ip-172-31-27-228 ~]$ sudo docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                               NAMES
3bb32075268e   namu123/front-end-app-1   "docker-entrypoint.s…"   55 seconds ago   Up 53 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   objective_allen
[ec2-user@ip-172-31-27-228 ~]$

```
![webapp-ec2hosting with docker](https://github.com/nazneenprojects/docker-webapp-deployment-AWS/assets/50543241/44f4d275-a2ed-489b-a89a-d64cf74e97a2)
