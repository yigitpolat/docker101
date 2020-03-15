# Step 3: Scale your service

In production we may need to handle large amounts of traffic to our application. So let's scale!

1. Update your service with an updated number of replicas

   We are going to use the `docker service` command to update the nginx service we created earlier to include 5 replicas. This is defining a new state for our service.

   ```bash
    docker service update --replicas=5 --detach=true nginx1
    # nginx1
   ```

   As soon as this command is run the following happens: 1\) The state of the service is updated to 5 replicas \(which is stored in the swarms internal storage\). 2\) Docker swarm recognizes that the number of replicas that is scheduled now does not match the declared state of 5. 3\) Docker swarm schedules 5 more tasks \(containers\) in an attempt to meet the declared state for the service.

   This swarm is actively checking to see if the desired state is equal to actual state, and will attempt to reconcile if needed.

2. Check the running instances

   After a few seconds, you should see that the swarm did its job, and successfully started 9 more containers. Notice that the containers are scheduled across all three nodes of the cluster. The default placement strategy that is used to decide where new containers are to be run is "emptiest node", but that can be changed based on your need.

   ```bash
    docker service ps nginx1
    : '
    ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
    iu3ksewv7qf9        nginx1.1            nginx:1.12          node1               Running             Running 17 minutes ago
    lfz1bhl6v77r        nginx1.2            nginx:1.12          node2               Running             Running 6 minutes ago
    qururb043dwh        nginx1.3            nginx:1.12          node3               Running             Running 6 minutes ago
    q53jgeeq7y1x        nginx1.4            nginx:1.12          node3               Running             Running 6 minutes ago
    xj271k2829uz        nginx1.5            nginx:1.12          node1               Running             Running 7 minutes ago
    '
   ```

3. Send a bunch of requests to [http://localhost:80](http://localhost:80)

   The `--publish 80:80` is still in effect for this service, that was not changed when we ran `docker service update`. However, now when we send requests on port 80, the routing mesh has multiple containers in which to route requests to. The routing mesh acts as a load balancer for these containers, alternating where it routes requests to.

   Let's try it out by curling multiple times. Note, that it doesn't matter which node you send the requests. There is no connection between the node that receives the request, and the node that that request is routed to.

   ```bash
    curl localhost:80
    # node3
    curl localhost:80
    # node3
    curl localhost:80
    # node2
    curl localhost:80
    # node1
    curl localhost:80
    # node1
   ```

   You should see which node is serving each request because of the nifty `--mount` command we used earlier.

   **Limits of the routing Mesh** The routing mesh can only publish one service on port 80. If you want multiple services exposed on port 80, then you can use an external application load balancer outside of the swarm to accomplish this.

4. Check the aggregated logs for the service

   Another easy way to see which nodes those requests were routed to is to check the aggregated logs. We can get aggregated logs for the service using `docker service logs [service name]`. This aggregates the output from every running container, i.e. the output from `docker container logs [container name]`.

   ```bash
    docker service logs nginx1
    : '
    nginx1.4.q53jgeeq7y1x@node3    | 10.255.0.2 - - [28/Jun/2017:18:59:39 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.52.1" "-"
    nginx1.2.lfz1bhl6v77r@node2    | 10.255.0.2 - - [28/Jun/2017:18:59:40 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.52.1" "-"
    nginx1.5.xj271k2829uz@node1    | 10.255.0.2 - - [28/Jun/2017:18:59:41 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.52.1" "-"
    nginx1.1.iu3ksewv7qf9@node1    | 10.255.0.2 - - [28/Jun/2017:18:50:23 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.52.1" "-"
    nginx1.1.iu3ksewv7qf9@node1    | 10.255.0.2 - - [28/Jun/2017:18:59:41 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.52.1" "-"
    nginx1.3.qururb043dwh@node3    | 10.255.0.2 - - [28/Jun/2017:18:59:38 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.52.1" "-"
    '
   ```

   Based on these logs we can see that each request was served by a different container.

   In addition to seeing whether the request was sent to node1, node2, or node3, you can also see which container on each node that it was sent to. For example `nginx1.5` means that request was sent to container with that same name as indicated in the output of `docker service ps nginx1`.

