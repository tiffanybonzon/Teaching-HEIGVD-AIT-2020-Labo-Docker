# Lab report - 04 Docker 

by Bonzon Tiffany, Scherer Laurent, Thoeny Laurent



### Introduction

### Table of content

### 

### Task 0: Identify issues and install the tools

*[M1]* : The configuration from the previous lab was specifying the servers in the config file, this is a problem as we expect scalability through a dynamic server poll. In the best case scenario we expect our load balancer to start or power off containers to match the current traffic. 

Another part of the problem is the issue with the web servers, killing a container and starting another one will force every client connected to that first web server to start a new session on the second one, to reconnect, maybe to re-add stuff to their cart. This is a significant inconvenience for the user.

At last, what could (and should) happen is that the newly created container take the address of the previous one, but I assume there are cases it could lead to a problem, we should not depend on a fixed IP address in the config.

*[M2]* : In order to add a new container to the server poll, we need to modify the configuration file for our proxy (`haproxy.cfg`), we need to add the new server in the `backend nodes` category. That forces us to update the config file in the docker template directory to allow the image to be built with the changes AND to update the current running containers by accessing their shell, editing the file then telling haproxy to consider the changes.

*[M3]* : A better approach would be to manage the containers dynamically, even if Haproxy doesn't kill or start any container based on the traffic, it still should be able to detect new containers and remove deleted one without any modification in his config.

*[M4]* : The best approach would be for the server containing the `webapp` to announce themselves as a service and for the load balancer to be able to detect the service containers automatically, we used `Traefik` and its label system in another class to do such a task. Another alternative would be to configure the server using an IP range and to assign the `webapp` containers to that range. 

*[M5]* : I don't think it'll be a problem to run a new service on our infrastructure, we can create a container with the service we want and either have that container contact the machines identified as `webapp` or add to the configuration of the containers configuration that they need to send their logs.

We'll typically prefer a pull solution in this case, we want our service to contact the containers and not the opposite. 

Alternatively we could consider implementing our new service on the load balancers, but we wouldn't recommend that since restarting one means restarting both services and it's better to separate the tasks.

*[M6]* : todo

### 

### Task 1: Add a process supervisor to run several processes

![](img/task1.png)

##### Describe your difficulties for this task and your understanding of what is happening during this task. Explain in your own words why are we installing a process supervisor. Do not hesitate to do more research and to find more articles on that topic to illustrate the problem.

We haven't faced any difficulties during the copy-paste of lines from the lab instructions to either our terminal or config files.

We are installing a process supervisor in order to overcome the difficulties of running multiple process in a single Docker container, those difficulties are not issued from our configuration but based on the way Docker was designed. We want to have more than one process running on our container so we make the supervisor the main process and we let him run the other services.

### Task 2: Add a tool to manage membership in the web server cluster

> 

### Difficulties

### Conclusion

