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

In order to copy the agent run script and make it executable, we added the following command in both our Dockerfiles (inspired by the way we handled ha).

```
# TODO: [Serf] Add Serf S6 setup
COPY services/serf /etc/services.d/serf
RUN chmod +x /etc/services.d/serf/run
```

Then we build the containers and verify their behavior through the browser and the logs, we see that the behavior is the one expected from the lab instructions where we get a failure to join the cluster at first, however we can see that the node then joined with the IP 192.168.42.42 (it's HA ip address) successfully.

![](img/task2_failure.png)

The next thing we did verify is to connect to s1 and to ping ha to verify if this behavior was indeed due to the name being properly resolved, it was.

![](img/task2_ping.png)

From this point the following indicated way to start the container was not implemented, since starting them with `docker-compose up --build` allowed us to have them connect properly.

*Since my behavior is different, let me specify my Docker version : Docker version 19.03.13-ce, build 4484c46d9d and docker-compose version 1.27.4*


##### Deliverables

The logs at the end of the second task have been exported to the corresponding folder, logs export were done using `docker logs <container> >> file`.

We haven't faced the problem where we need to start `s1` and `s2` before starting `ha`, however it's obviously a big problem if you need to link the containers that way, future containers wouldn't be able to reach `ha` because of the same problem. The easier solution is to allow the containers to resolve ha.

The way `Serf` works is with a `gossip protocol`, it's a way to design the communication that has his name issued from the analogy of workers spreading rumors, everyone periodically talks to someone else randomly and the information therefore spreads. There is another analogy that calls such a communication scheme an `epidemic protocol`, as a parallel can be made between the way the information spread and a virus would spread
(source)[https://en.wikipedia.org/wiki/Gossip_protocol].

One of the other way for the discovery to happen on Docker is the way used by `Traefik` (already used as an example earlier), Traefik discovery uses an access to the Docker API to detect running containter and information on these containers to detect the services. In our case we would allow `ha` to use the Docker API and tell him that every container with a LABEL `backend` is a container running the webapp.

The fondamental difference between these approach is that the way we configure the discovery from our proxy instance is not different from how we could do it with an infrastructure not based on Docker, where the method using `Docker API` is obviously based on using Docker.

### Task 3: React to membership changes



### Difficulties

### Conclusion

