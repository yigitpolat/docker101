# Step 1: Create your first swarm

In this step, we will create our first swarm using play-with-docker.

1. Navigate to [http://play-with-docker.com](http://play-with-docker.com)
2. Click "**add new instance**" on the lefthand side three times to create three nodes. Our first swarm cluster will have three nodes.
3. Initialize the swarm on node 1

   ```bash
    docker swarm init --advertise-addr eth0
   ```

   ```bash
    > Swarm initialized: current node (ss0mij8fk3apjwd7oqzfv7jlb) is now a manager.

    > To add a worker to this swarm, run the following command:

    > docker swarm join --token SWMTKN-1-02lbbllp3aztmuy6s35ndrbz67rwsiwyinm16yweuegvxj1gbn-bot1jxqwdt91ik8l5s805k35a 192.168.0.46:2377

    > To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
   ```

4. To add a worker to this swarm, modify the following command for your conditions and run it:

   ```bash
    docker swarm join \ 
      --token SWMTKN-1-02lbbllp3aztmuy6s35ndrbz67rwsiwyinm16yweuegvxj1gbn-bot1jxqwdt91ik8l5s805k35a \
      192.168.0.46:2377
   ```

   To add a manager to this swarm, run `docker swarm join-token manager` and follow the instructions.

   You can think of docker swarm as a special "mode" that is activated by the command: `docker swarm init`. The `--advertise-addr` specifies the address in which the other nodes will use to join the swarm.

   This `docker swarm init` command generates a join token. The token makes sure that no malicious nodes join our swarm. We will need to use this token to join the other nodes to the swarm. For convenience, the output includes the full command `docker swarm join` which you can just copy/paste to the other nodes.

5. On both node2 and node3, copy and run the `docker swarm join` command that was outputted to YOUR console by the last command.

   You now have a three node swarm!

6. Back on node1, run `docker node ls` to verify your 3 node cluster.

   ```bash
    docker node ls
   ```

   ```bash
    > ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
    > 7x9s8baa79l29zdsx95i1tfjp     node3               Ready               Active
    > x223z25t7y7o4np3uq45d49br     node2               Ready               Active
    > zdqbsoxa6x1bubg3jyjdmrnrn *   node1               Ready               Active              Leader
   ```

   This command outputs the three nodes in our swarm. The `*` next to the ID of the node represents the node that handled that specific command \(`docker node ls` in this case\).

   Our node consists of 1 manager node and 2 workers nodes. Managers handle commands and manage the state of the swarm. Workers cannot handle commands and are simply used to run containers at scale. By default, managers are also used to run containers.

   All `docker service` commands for the rest of this lab need to be executed on the manager node \(Node1\).

   **Note:** While we will control the Swarm directly from the node in which its running, you can control a docker swarm remotely by connecting to the docker engine of the manager via the remote API or activating a remote host from your local docker installation \(using the `$DOCKER_HOST` and `$DOCKER_CERT_PATH` environment variables\). This will become useful when you want to control production applications remotely instead of ssh-ing directly into production servers.

