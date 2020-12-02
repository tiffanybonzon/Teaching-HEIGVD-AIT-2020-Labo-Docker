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

*[M4]* : The best approach would be for the server containing the `webapp` to announce themselves as a service and for the load balancer to be able to detect the service containers automatically. Another alternative would be to configure the server using an IP range and to assign the `webapp` containers to that range. 



### Difficulties

### Conclusion

