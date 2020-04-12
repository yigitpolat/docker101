# Step 6: Understanding Image Layers

One of the major design properties of Docker is its use of the union file system.

Consider the Dockerfile that we created before:

```text
FROM python:3-alpine
RUN pip install flask
CMD ["python","app.py"]
COPY app.py /app.py
```

Each of these lines is a **layer**. Each layer contains only the delta, or changes from the layers before it. To put these layers together into a single running container, Docker makes use of the union file system to overlay layers transparently into a single view.

Each layer of the image is **read-only**, except for the very top layer which is created for the container. The read/write container layer implements "copy-on-write" which means that files that are stored in lower image layers are pulled up to the read/write container layer only when edits are being made to those files. Those changes are then stored in the container layer. The "copy-on-write" function is very fast, and in almost all cases, does not have a noticeable effect on performance. You can inspect which files have been pulled up to the container level with the `docker diff` command. More information about how to use `docker diff` can be found [here](https://docs.docker.com/engine/reference/commandline/diff/) .

![](https://github.com/volaka/intro-to-docker-lab/tree/da2a591b53ebff09bafd4eadbc4bf9ab5efd9c1a/.gitbook/assets/lab2_understanding_image_layers_1.png)

Since image layers are read-only, they can be shared by images and by running containers. For instance, creating a new python app with its own Dockerfile with similar base layers, would share all the layers that it had in common with the first python app.

```text
FROM python:3.6.1-alpine
RUN pip install flask
CMD ["python","app2.py"]
COPY app2.py /app2.py
```

![](https://github.com/volaka/intro-to-docker-lab/tree/da2a591b53ebff09bafd4eadbc4bf9ab5efd9c1a/.gitbook/assets/lab2_understanding_image_layers_2.png)

You can also experience the sharing of layers when you start multiple containers from the same image. Since the containers use the same read-only layers, you can imagine that starting up containers is very fast and has a very low footprint on the host.

You may notice that there are duplicate lines in this Dockerfile and the Dockerfile you created earlier in this lab. Although this is a very trivial example, you can pull common lines of both Dockerfiles into a "base" Dockerfile, that you can then point to with each of your child Dockerfiles using the `FROM` command.

Image layering enables the docker caching mechanism for builds and pushes. For example, the output for your last `docker push` shows that some of the layers of your image already exists on the Docker Hub.

```bash
docker push $DOCKER_USERNAME/python-hello-world
```

```bash
: '
> The push refers to a repository [docker.io/<DOCKER_USERNAME>/python-hello-world]
> 94525867566e: Pushed 
> 64d445ecbe93: Layer already exists 
> 18b27eac38a1: Layer already exists 
> 3f6f25cd8b1e: Layer already exists 
> b7af9d602a0f: Layer already exists 
> ed06208397d5: Layer already exists 
> 5accac14015f: Layer already exists 
> latest: digest: sha256:91874e88c14f217b4cab1dd5510da307bf7d9364bd39860c9cc8688573ab1a3a size: 1786
'
```

To look more closely at layers, you can use the `docker image history` command of the python image we created.

```bash
docker image history python-hello-world
```

```bash
: '
> IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
> f1b2781b3111        5 minutes ago       /bin/sh -c #(nop) COPY file:0114358808a1bb...   159B                
> 0ab91286958b        5 minutes ago       /bin/sh -c #(nop)  CMD ["python" "app.py"]      0B                  
> ce41f2517c16        5 minutes ago       /bin/sh -c pip install flask                    10.6MB              
> c86415c03c37        8 days ago          /bin/sh -c #(nop)  CMD ["python3"]              0B                  
> <missing>           8 days ago          /bin/sh -c set -ex;   apk add --no-cache -...   5.73MB              
> <missing>           8 days ago          /bin/sh -c #(nop)  ENV PYTHON_PIP_VERSION=...   0B                  
> <missing>           8 days ago          /bin/sh -c cd /usr/local/bin  && ln -s idl...   32B                 
> <missing>           8 days ago          /bin/sh -c set -ex  && apk add --no-cache ...   77.5MB              
> <missing>           8 days ago          /bin/sh -c #(nop)  ENV PYTHON_VERSION=3.6.1     0B                  
> <missing>           8 days ago          /bin/sh -c #(nop)  ENV GPG_KEY=0D96DF4D411...   0B                  
> <missing>           8 days ago          /bin/sh -c apk add --no-cache ca-certificates   618kB               
> <missing>           8 days ago          /bin/sh -c #(nop)  ENV LANG=C.UTF-8             0B                  
> <missing>           8 days ago          /bin/sh -c #(nop)  ENV PATH=/usr/local/bin...   0B                  
> <missing>           9 days ago          /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B                  
> <missing>           9 days ago          /bin/sh -c #(nop) ADD file:cf1b74f7af8abcf...   4.81MB
'
```

Each line represents a layer of the image. You'll notice that the top lines match to your Dockerfile that you created, and the lines below are pulled from the parent python image. Don't worry about the "\" tags. These are still normal layers; they have just not been given an ID by the docker system.

