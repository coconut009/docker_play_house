__<h3>System</h3>__
+ CPU: AMD Ryzen 9 5900x
+ RAM: 64GB
+ GPU: AMD 6800XT
+ OS: Ubuntu 20.04
+ Kernel Version: 5.13.0-39-generic
+ Docker: Docker version 20.10.13, build a224086


__<h3>Setup Docker Environment</h3>__
+ Install the docker from Docker website (DO not use the install from package manager)
    `curl -fsSL https://get.docker.com -o get-docker.sh`
    `sh get-docker.sh `
+ Option want to use Docker as non-root user:
    
    `sudo usermod -a -G docker $USER`

    <!-- more --> 
+ Install Docker compose 
   `DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}`

   `mkdir -p $DOCKER_CONFIG/cli-plugins`

   ***Check the docker compose version from the <a href= "https://github.com/docker/compose">docker github page</a>
   Choose the compose version as you preferred*** 

   `curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose`

   `chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose`

+ After the docker environment is set use the following command to check the docker information
    `docker version`

    `docker info`

+ Docker command line structure
   `docker <management_command> <execution_subcommand`


__<h4>Notes</h4>__
Image and container difference
images: application we want to run<br>
container is an instance of that image running as a process
multiple containers can of the same image 

__<h3>Start Building the container</h3>__
* Docker container with desktop environment<br>
This is a sample <a href = "https://github.com/coconut009/docker_play_house/blob/main/xrdp_base_Dockerfile"> docker container: ubuntu20:04 xrdp xfce-desktop</a>
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


__<h3>check the running containers </h3>__
* list all the running containers 
   * ` docker ps `
* list all the containers include the stopped containers
   * `docker ps -all`
* list container ID
   * `docker container ls -q | awk '{print $1}'`
* list container process ID by given container ID
   * `docker inspect --format '{{ .State.Pid }} <container_ID>`
  
__<h3>Common used Command for Docker container </h3>__
* Stop container by the container ID
 * `docker stop <container_ID>`
* Stop container by the docker image's names
 * `docker ps -q --filter ancestor=<container_image_name> | xargs docker stop `
* Stop all running containers 
 * `docker stop $(docker ps -q)`
 * clean all the exited containers
    * `docker system prune`
 * remove specific container
    * `docker container rm <container_ID>` 
* process list in one container
 * `docker container top`
* Details of one container config
 * `docker container inspect`
* Performance stats for all containers
 * `docker container stats`
* Start new container interactively
 * `docker container run -it`
  To access a running container/ runn addition command in existing container 
   * `docker exec -it <docker_image> /bin/bash`


__<h3><a href="https://docs.docker.com/config/containers/resource_constraints/?msclkid=b9335724bf2c11eca68895b363e44d8f">Docker runtime options with Memory, CPUs </a></h3>__
1. use "-m" or "--memory" option to limit the maximum amount memory  the container can use 
2. use "--cpuset-cpus" option to specify the logical CPU core allocate to the container
3. use "--cpus=<value>" option to specify how much of the available CPU resources a container can use


__<h3><a href="https://docs.docker.com/network/">Docker Networks</a></h3>__

* show docker networks
 * `docker network ls`
* Inspect a network
 * `docker network inspect`
* Create a network
 * `docker network create --driver`
* Attach a network to container
 * `docker network connect`
* Detach a network from container 
   * `docker network disconnect`
  
Network bridge: Default Docker virtual network which is NAT network behind the host IP <br>
Network host: It gains performance by skipping virtual networks but sacrifices security of containers model<br>
Network none: Removes eth0 and only leaves with localhost interface in container
Network driver: Built-in or 3rd party extensions that gives you virtual network features <br>
Docker network connect: Dynamically creates a NIC in a container on an existing virtual network <br>
Docker Network Security:<br>
* Create apps fo fronted/backend sit on same Docker network
* The inter-communication never leaves host
* All externally exposed ports closed by default
* You must manually expose port via '-p' option
  
Docker Network: DNS




__<h3>Docker with Vulkan API (<a href ="https://amdgpu-install.readthedocs.io/en/latest/install-script.html"> AMD GPU</a>)</h3>__
Running game with Vulkan API inside docker containers need to pass several environment variables

If the host graphic driver is AMD Proprietary driver make sure you passed the following environment variable when run/create the container<br>
* `-e VK_ICD_FILENAMES=/etc/vulkan/icd.d/amd_icd32.json:/etc/vulkan/icd.d/amd_icd64.json`

If the above environment variables are not sufficient try add this 
* `-v /usr/lib:/usr/lib`