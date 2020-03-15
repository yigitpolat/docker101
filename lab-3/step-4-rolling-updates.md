# Step 4: Rolling Updates

## 

Now that we have our service deployed, let's demonstrate a release of our application. We are going to update the version of Nginx to version "1.13". To do this update we are going to use the `docker service update` command.

```bash
$ docker service update --image nginx:1.13 --detach=true nginx1
```

This will trigger a rolling update of the swarm. Quickly type in `docker service ps nginx1` over and over to see the updates in real time.

You can fine tune the rolling update using these options: `--update-parallelism` will dictate the number of containers to update at once. \(defaults to 1\) `--update-delay` will dictate the delay between finishing updating a set of containers before moving on to the next set.

After a few seconds, run `docker service ps nginx1` to see all the images have been updated to nginx:1.13.

```bash
$ docker service ps nginx1
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STAT
E             ERROR               PORTS
di2hmdpw0j0z        nginx1.1            nginx:1.13          node1               Running             Running 50 s
econds ago
iu3ksewv7qf9         \_ nginx1.1        nginx:1.12          node1               Shutdown            Shutdown 52
seconds ago
qsk6gw43fgfr        nginx1.2            nginx:1.13          node2               Running             Running 47 s
econds ago
lfz1bhl6v77r         \_ nginx1.2        nginx:1.12          node2               Shutdown            Shutdown 49
seconds ago
r4429oql42z9        nginx1.3            nginx:1.13          node3               Running             Running 41 s
econds ago
qururb043dwh         \_ nginx1.3        nginx:1.12          node3               Shutdown            Shutdown 43
seconds ago
jfkepz8tqy9g        nginx1.4            nginx:1.13          node2               Running             Running 44 s
econds ago
q53jgeeq7y1x         \_ nginx1.4        nginx:1.12          node3               Shutdown            Shutdown 45
seconds ago
n15o01ouv2uf        nginx1.5            nginx:1.13          node3               Running             Running 39 s
econds ago
xj271k2829uz         \_ nginx1.5        nginx:1.12          node1               Shutdown            Shutdown 40
seconds ago
```

You have successfully updated your app to the latest version of nginx!

## 

