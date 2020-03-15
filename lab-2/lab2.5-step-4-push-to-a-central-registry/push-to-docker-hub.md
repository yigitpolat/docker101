# Push to Docker Hub

## Create a Docker Hub account

Navigate to [https://hub.docker.com](https://hub.docker.com) and create an account if you haven't already. For this lab we will be using the docker hub as our central registry. Docker hub is a free service to store publicly available images, or you can pay to store private images. Go to the [DockerHub](https://hub.docker.com) website and create a free account. Most organizations that use docker heavily will set up their own registry internally. To simplify things, we will be using the Docker Hub, but the following concepts apply to any registry.

## Login on Terminal

Login You can log into the docker registry account by typing `docker login` on your terminal.

```bash
docker login
#Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
# Username:>
```

## Tag your image with your username

The Docker Hub naming convention is to tag your image with \[dockerhub username\]/\[image name\]. To do this, we are going to tag our previously created image `python-hello-world` to fit that format.

Enter your username as an environment varibale:

```bash
export DOCKER_USERNAME=[dockerhub username]
```

```bash
docker tag python-hello-world $DOCKER_USERNAME/python-hello-world
```

## Push your image to the registry

Once we have a properly tagged image, we can use the `docker push` command to push our image to the Docker Hub registry.

```bash
docker push $DOCKER_USERNAME/python-hello-world
```

```bash
: '
> The push refers to a repository [docker.io/<DOCKER_USERNAME>/python-hello-world]
> 2bce026769ac: Pushed 
> 64d445ecbe93: Pushed 
> 18b27eac38a1: Mounted from library/python 
> 3f6f25cd8b1e: Mounted from library/python 
> b7af9d602a0f: Mounted from library/python 
> ed06208397d5: Mounted from library/python 
> 5accac14015f: Mounted from library/python 
> latest: digest: sha256:508238f264616bf7bf962019d1a3826f8487ed6a48b80bf41fd3996c7175fd0f size: 1786
'
```

## Check out your image on docker hub in your browser

Navigate to [https://hub.docker.com](https://hub.docker.com) and go to your profile to see your newly uploaded image.

Now that your image is on Docker Hub, other developers and operations can use the `docker pull` command to deploy your image to other environments.

**Note:** Docker images contain all the dependencies that it needs to run an application within the image. This is useful because we no longer have deal with environment drift \(version differences\) when we rely on dependencies that are install on every environment we deploy to. We also don't have to go through additional steps to provision these environments. Just one step: install docker, and you are good to go.

