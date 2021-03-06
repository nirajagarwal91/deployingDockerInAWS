# deployingDockerInAWS


1. To run a Docker Container 
  `docker run [container-name]` E.g `docker run hello-world`
  Docker will pull these images form docker hub if not present. Everytime a docker run command is run it wil create a new container with new id.
  
2. To See all the docker containers (both running and not running)
  `docker ps -a`

3. To run docker with interactive shell `docker run -ti --rm ubuntu bash`
    --rm flag will remove the Docker container when done with it
    --ti will give an interactive shell. In this case for ubuntu

4. So what is a docker image? 
    Docker images are minimalistic software that is installed and bundled up to run your program. For example: To run a web page on       docker, you need to install all the dependancies for the front end. 
    
5. Docker run command takes an image and converts it into a living running container. So any file created in the container will stay in the container. If a container is fired from the same image, created file in the previous container will not be present. 

6. Docker Commit command will create images out of the containers. Any file that is created or modified in the working container can be put into an image using a docker commit command. NOTE: It will create a new image.
  `docker commit [Container ID]` 
  This return a SHA256 encrypted image id, which is difficult to remember. To tag now use 
  `docker tag [SHA256 ID got from docker commit] [name of image(user value)]`

7. Above step can be skipped with container id. Just type `docker commit [Container_Name] [New_Image_Name]`

8. Docker attach of containers `docker attach [container_name]`
9. `docker run -d -ti ubuntu bash` this will keep running a ubuntu container. -d means detach.
10. To trouble shoot docker `docker logs [container_name]`

11. `docker rm [container_name]` This is to avoid the error when you get something that says that container with the name already exists.

12. To make a docker as communicator. It has two ports open 45678 and 45679.
  `docker run -ti --rm -p 45678:45678 -p 45679:45679 --name echo-server ubuntu:14.04 bash`
  In Bash run `nc -lp 45678 | nc -lp 45679`. nc is the command for netcat to open up -lp (listening ports)
  In other terminals run the server `docker run -ti --rm ubuntu:14.04 bash`
  Find the IP address of the docker server which has open ports and then do 
  `nc <ip_address> 45678` for sender
  `nc <ip_address> 45679` for receiver
  *****************See the magic happen then ********************************
  
13. Well its better to allocate the outside port dynamically. 
14. `docker port [container_name]` will show all the inside port connect to which port in outside server.
15. `docker run -ti --rm --name server ubuntu:14.04 bash`
    On Bash : `nc -lp 1234` Server port opened
    On other terminal: `docker run -ti --rm --link client --name server ubuntu:14.04 bash`
    On Bash: nc server [portName]
    
    *** This link would break if ip address change ***
 
 16. To avoid the above scenario, use a network. 
 
 17. DOCKER iMAGES `docker rmi [image_name]` to remove image
 
 18. Dokcer volume are like virtual disks. Are of two types 1. Persistent 2. Ephemeral (present only when container is in use)
 19.  Sharing DATA between containers
      `docker run -ti --rm -v /home/niraj/shared-host:/shared-folder-container ubuntu bash`
      Create a file in the container in the /shared-folder-container.
      Once the Container exits, the file can still be seen in /home/niraj/shared-host. 
      
  20. Passing data from once container to another. Please Note: This is Ephemeral
      `docker run -ti --volumes-from [volume name] ubuntu bash`
  
  21. `docker search [search item]` on command line. 
  
  22. Build Docker files `docker build -t [name-of-the-image] .` <- . is the path where the docker file is.
      `docker run --rm -ti [name-of-the-image-build]`
      
  23. Docker follows a client-server architecture form. It is possible to call a docker server from a docker client that 
  resides inside a docker container. 
  #`docker run --rm -ti -v /var/run.docker.sock:/var/run.docker.sock docker sh`
  Here docker.sock file has the socker information for communication. The file is placed inside the new container at the same location.
  
  24. Docker uses ### bridges to create virtual network in your computer. 
      `docker run --rm -ti --net=host ubuntu:16.04 bash` --net = host gives container direct access to networking.
     In Bash `apt-get update && apt-get install bridge-utils`
     Create a network `docker create network [netwotk-name]`
  
  25. It uses the built-in firewall features of the Linux kernel, namely the iptables command, to create firewall rules that control when packets get sent between the bridges and thus become available to the containers that are attached to those bridges. This whole system is commonly referred to as NAT, or Network Address Translation. That means when a packet is on its way out towards the internet, you change the source address, so it'll come back to you. And then when it's on the way back in, you change the destination address, so it looks like it came directly from the machine you were connecting to.
  `sudo iptables -n -L -t nat`
  How docker shares packets between containers 
  
  `docker run --rm -ti --net=host --privileged=true ubuntu bash`
  Install iptables on this container. Run another container that has a port open. `docker run --rm -ti -p 8080:8080 ubuntu bash` 
  
  26. Mounting and unmounting directories but first need to spin up a container with --privileged=true flag
    `mount -o bind [first-folder] [mounting-folder]`
    This will mount the contents of first-folder on the mounting-folder. 
    To Unmount `unmount [mounitng-folder]`
    
    # Mounting volume is mounitng the host file system over the guest file system
    
  27. Docker Registry. Listens on port 5000
      To run Registry `docker run -d -p 5000:5000 --restart=always --name registry registry:2`
      Using the docker push command `docker push [tag of the container]`
      This will save the registry.
      
      Another cool way is to save images locally. 
      
      `docker save -o filename.tar.gz [imagename:tag, ...]`
       
       To load these images 
       
       `docker load -i filename.tar.gz`
