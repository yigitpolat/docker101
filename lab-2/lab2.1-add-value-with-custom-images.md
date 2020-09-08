# Add value with custom images

## Overview

In this lab, we build on our knowledge from lab 1 where we used Docker commands to run containers. We will create a custom Docker Image built from a Dockerfile. Once we build the image, we will push it to a central registry where it can be pulled to be deployed on other environments. Also, we will briefly describe image layers, and how Docker incorporates "copy-on-write" and the union file system to efficiently store images and run containers.

We will be using a few Docker commands in this lab. For full documentation on available commands check out the [official documentation](https://docs.docker.com/).
