# Step 2: Deploy your first service

Now that we have our 3 node Swarm cluster initialized, let's deploy some containers. To run containers on a Docker Swarm, we want to create a service. A service is an abstraction that represents multiple containers of the same image deployed across a distributed cluster.

Let's do a simple example using Nginx. For now we will create a service with just 1 running container, but we will scale up later.

1. Deploy a service using Nginx

   ```bash
   $ docker service create --detach=true --name nginx1 --publish 80:80  --mount source=/etc/hostname,target=/usr/share/nginx/html/index.html,type=bind,ro nginx:1.12
   pgqdxr41dpy8qwkn6qm7vke0q
   ```

    This above statement is _declarative_, and docker swarm will actively try to maintain the state declared in this command unless explicitly changed via another `docker service` command. This behavior comes in handy when nodes go down, for example, and containers are automatically rescheduled on other nodes. We will see a demonstration of that a little later on in this lab.

    The `--mount` flag is a neat trick to have nginx print out the hostname of the node it's running on. This will come in handy later in this lab when we start load balancing between multiple containers of nginx that are distributed across different nodes in the cluster, and we want to see which node in the swarm is serving the request.

    We are using nginx tag "1.12" in this command. We will demonstrate a rolling update with version 1.13 later in this lab.

    The `--publish` command makes use of the swarm's built in _routing mesh_. In this case port 80 is exposed on _every node in the swarm_. The routing mesh will route a request coming in on port 80 to one of the nodes running the container.

2. Inspect the service

    You can use `docker service ls` to inspect the service you just created.

    ```bash
    docker service ls
    : 'ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
    pgqdxr41dpy8        nginx1              replicated          1/1                 nginx:1.12          *:80->80/tcp
    '
    ```

3. Check out the running container of the service

    To take a deeper look at the running tasks, you can use `docker service ps`. A task is yet another abstraction using in docker swarm that represents the running instances of a service. In this case, there is a 1-1 mapping between a task and a container.

    ```bash
    docker service ps nginx1
    : '
    ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STAT
    E           ERROR               PORTS
    iu3ksewv7qf9        nginx1.1            nginx:1.12          node1               Running             Running 8 mi
    nutes ago
    '
    ```

    If you happen to know which node your container is running on \(you can see which node based on the output from `docker service ps`\), you can use `docker container ls` to see the container running on that specific node.

4. Test the service

    Because of the routing mesh, we can send a request to any node of the swarm on port 80. This request will be automatically routed to the one node that is running our nginx container.

    Try this on each node:

    ```bash
    curl localhost:80
    # node1
    ```

    Curling will output the hostname where the container is running. For this example, it is running on "node1", but yours might be different.