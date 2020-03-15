# Summary

In this lab, you got an introduction to problems that come with running container with production such as scheduling services across distributed nodes, maintaining high availability, implementing reconciliation, scaling, and logging. We used the orchestration tool that comes built-in to the Docker Engine- Docker Swarm, to address some of these issues.

Key Takeaways

* The Docker Swarm schedules services using a declarative language. You declare the state, and the swarm attempts to maintain and reconcile to make sure the actual state == desired state
* Docker Swarm is composed of manager and worker nodes. Only managers can maintain the state of the swarm and accept commands to modify it. Workers have high scability and are only  used to run containers. By default managers can run containers as well.
* The routing mesh built into swarm means that any port that is published at the service level will be exposed on every node in the swarm. Requests to a published service port will be routed automatically to a container of the service that is running in the swarm.
* Many tools out there exist to help solve problems with orchestration containerized applications in production, include Docker Swarm, and the [IBM Cloud Kubernetes Service](https://www.ibm.com/cloud/container-service/).

