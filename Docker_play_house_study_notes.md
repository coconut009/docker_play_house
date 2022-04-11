__<h3>System</h3>__
+ CPU: AMD Ryzen 9 5900x
+ RAM: 64GB
+ GPU: AMD 6800XT
+ OS: Ubuntu 20.04
+ Kernel Version: 5.13.0-39-generic
+ Docker: Docker version 20.10.13, build a224086


__<h3>Setup Docker Environment</h3>__
1. Install the docker from Docker website (DO not use the install from package manager)

    `curl -fsSL https://get.docker.com -o get-docker.sh`

    `sh get-docker.sh `
2. Option want to use Docker as non-root user:
    
    `sudo usermod -a -G docker $USER`
3. Install Docker compose 
   `DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}`

   `mkdir -p $DOCKER_CONFIG/cli-plugins`

   ***Check the docker compose version from the <a herf= "https://github.com/docker/compose">docker github page</a>
   Choose the compose version as you preferred*** 

   `curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose`

   `chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose`

4. After the docker environment is set use the following command to check the docker information
    `docker version`

    `docker info`

5. Docker command line structure
   `docker <management_command> <execution_subcommand`


__<h4>Notes</h4>__
Image and container difference
images: application we want to run<br>
container is an instance of that image running as a process
multiple containers can of the same image 

__<h3>Start Building the </h3>__
* Docker container with desktop environment<br>
This is a sample <a herf = "https://github.com/coconut009/docker_play_house/blob/main/xrdp_base_Dockerfile"> docker container: ubuntu20:04 xrdp xfce-desktop</a>
  * To build the container for more details check <a href="https://docs.docker.com/engine/reference/commandline/image_build/"> docker document reference page</a>
    * `docker build . -t <image_name>`  
  * Run the container 
    * `docker run -d -p 3389 <docker_image_name>`
  * Run the container with AMD GPU hardware acceleration 
    * `docker run --rm -d -p 3389 --device=/dev/dri --device /dev/snd -v /opt:/opt -v /etc/amd:/etc/amd -v /etc/OpenCL:/etc/OpenCL -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY <docker_image_name>`
  * Connect container with RDP session
    * run the following command to check the port number on the host<br>
             `docker ps`
    * sample output: <br>
            `root@Docker:~# docker ps` <br>
`CONTAINER ID   MAGE    COMMAND CREATED STATUS  PORTS   NAMES`  <br>
`8570dee06e52   xrdp_base:01   "/usr/bin/run.sh"   47 hours ago   Up 47 hours   0.0.0.0:49184->3389/tcp, :::49184->3389/tcp   relaxed_margulis`

    * Launch RDP (Windows environment) or Remmina (Linux environment)
      * connect to the host IP address with the port number 
      * Example: 192.168.1.63:49184 
      * Use the credential: root/root 