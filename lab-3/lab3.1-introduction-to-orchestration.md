# Introduction to orchestration

## Overview

So far you have learned how to run applications using docker on your local machine, but what about running dockerized applications in production? There are a number of problems that come with building an application for production: scheduling services across distributed nodes, maintaining high availability, implementing reconciliation, scaling, and logging... just to name a few.

There are several orchestration solutions out there that help you solve some of these problems. One example is the [IBM Cloud Kubernetes Service](https://www.ibm.com/cloud/container-service/) which uses [Kubernetes](https://kubernetes.io/) to run containers in production.

Before we introduce you to Kubernetes, we will teach you how to orchestrate applications using Docker Swarm. Docker Swarm is the orchestration tool that comes built-in to the Docker Engine.

We will be using a few Docker commands in this lab. For full documentation on available commands check out the [official documentation](https://docs.docker.com/).

### Prerequisites

In order to complete a lab about orchestrating an application that is deployed across multiple hosts, you need... well, multiple hosts. To make things easier, for this lab we will be using the multi-node support provided by [http://play-with-docker.com](http://play-with-docker.com). This is the easiest way to test out Docker Swarm, without having to deal with installing docker on multiple hosts yourself.

