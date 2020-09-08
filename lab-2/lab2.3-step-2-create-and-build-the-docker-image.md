# Step 2: Create and build the Docker Image

If you don't have python install locally, don't worry! Because you don't need it. One of the advantages of using Docker containers is that you can build python into your containers, without having python installed on your host.

## Create a Dockerfile

Create a `Dockerfile` but running the following command. \(copy-paste the entire code block\)

```bash
echo 'FROM python:3-alpine
RUN pip install flask
CMD ["python","app.py"]
COPY app.py /app.py' > Dockerfile
```

A Dockerfile lists the instructions needed to build a docker image. Let's go through the above file line by line.

### FROM python:3-alpine

This is the starting point for your Dockerfile. Every Dockerfile must start with a `FROM` line that is the starting image to build your layers on top of. In this case, we are selecting the `python:3-alpine` base layer since it already has the version of python and pip that we need to run our application. The `alpine` version means that it uses the alpine distribution, which is significantly smaller than an alternative flavor of linux. A smaller image means it will download \(deploy\) much faster, and it also has advantages for security because it has a smaller attack surface.

Here we are using the "3-alpine" tag for the python image. Take a look at the available tags for the official python image on the [Docker Hub](https://hub.docker.com/_/python/). It is best practice to use a specific tag when inheriting a parent image so that changes to the parent dependency are controlled. If no tag is specified, the "latest" tag takes into effect, which is acts as a dynamic pointer that points to the latest version of an image.

For security reasons, it is very important to understand the layers that you build your docker image on top of. For that reason, it is highly recommended to only use "official" images found in the [docker hub](https://hub.docker.com/), or non-community images found in the Docker Hub. These images are [vetted](https://docs.docker.com/docker-hub/official_repos/) to meet certain security requirements, and also have very good documentation for users to follow. You can find more information about this [python base image](https://hub.docker.com/images/python), as well as all other images that you can use, on the [Docker Hub](https://hub.docker.com/).

For a more complex application you may find the need to use a`FROM` image that is higher up the chain. For example, the parent [Dockerfile](https://github.com/docker-library/python/blob/88ba87d31a3033d4dbefecf44ce25aa1b69ab3e5/3.6/alpine/Dockerfile) for our python app starts with `FROM alpine`, then specifies a series of `CMD` and `RUN` commands for the image. If you needed more fine-grained control, you could start with `FROM alpine` \(or a different distribution\) and run those steps yourself. To start off though, I recommend using an official image that closely matches your needs.

### RUN pip install flask

The `RUN` command executes commands needed to set up your image for your application, such as installing packages, editing files, or changing file permissions. In this case we are installing flask. The `RUN` commands are executed at build time, and are added to the layers of your image.

### CMD \["python","app.py"\]

`CMD` is the command that is executed when you start a container. Here we are using `CMD` to run our python app.

There can be only one `CMD` per Dockerfile. If you specify more thane one `CMD`, then the last `CMD` will take effect. The parent python:3.6.1-alpine also specifies a `CMD` \(`CMD python2`\). You can find the Dockerfile for the official python:alpine image [here](https://github.com/docker-library/python/blob/88ba87d31a3033d4dbefecf44ce25aa1b69ab3e5/3.6/alpine/Dockerfile).

You can use the official python image directly to run python scripts without installing python on your host. But today, we are creating a custom image to include our source, so that we can build an image with our application and ship it around to other environments.

### COPY app.py /app.py

This copies the app.py in the local directory \(where you will run `docker image build`\) into a new layer of the image. This instruction is the last line in the Dockerfile. Layers that change frequently, such as copying source code into the image, should be placed near the bottom of the file to take full advantage of the Docker layer cache. This allows us to avoid rebuilding layers that could otherwise be cached. For instance, if there was a change in the `FROM` instruction, it would invalidate the cache for all subsequent layers of this image. We will demonstrate a this little later in this lab.

It seems counter-intuitive to put this after the `CMD ["python","app.py"]` line. Remember, the `CMD` line is executed only when the container is started, so we won't get a `file not found` error here.

And there you have it: a very simple Dockerfile. A full list of commands you can put into a Dockerfile can be found [here](https://docs.docker.com/engine/reference/builder/). Now that we defined our Dockerfile, let's use it to build our custom docker image.

## Build the docker image

Pass in `-t` to name your image `python-hello-world`.

```bash
docker image build -t python-hello-world .
```

```bash
> Sending build context to Docker daemon  3.072kB
> Step 1/4 : FROM python:3.6.1-alpine
> 3.6.1-alpine: Pulling from library/python
> acb474fa8956: Pull complete 
> 967ab02d1ea4: Pull complete 
> 640064d26350: Pull complete 
> db0225fcac8f: Pull complete 
> 5432cc692c60: Pull complete 
> Digest: sha256:768360b3fad01adffcf5ad9eccb4aa3ccc83bb0ed341bbdc45951e89335082ce
> Status: Downloaded newer image for python:3.6.1-alpine
>  ---> c86415c03c37
> Step 2/4 : RUN pip install flask
>  ---> Running in cac3222673a3
> Collecting flask
>   Downloading Flask-0.12.2-py2.py3-none-any.whl (83kB)
> Collecting itsdangerous>=0.21 (from flask)
>   Downloading itsdangerous-0.24.tar.gz (46kB)
> Collecting click>=2.0 (from flask)
>   Downloading click-6.7-py2.py3-none-any.whl (71kB)
> Collecting Werkzeug>=0.7 (from flask)
>   Downloading Werkzeug-0.12.2-py2.py3-none-any.whl (312kB)
> Collecting Jinja2>=2.4 (from flask)
>   Downloading Jinja2-2.9.6-py2.py3-none-any.whl (340kB)
> Collecting MarkupSafe>=0.23 (from Jinja2>=2.4->flask)
>   Downloading MarkupSafe-1.0.tar.gz
> Building wheels for collected packages: itsdangerous, MarkupSafe
>   Running setup.py bdist_wheel for itsdangerous: started
>   Running setup.py bdist_wheel for itsdangerous: finished with status \'done\'
>   Stored in directory: /root/.cache/pip/wheels/fc/a8/66/24d655233c757e178d45dea2de22a04c6d92766abfb741129a
>   Running setup.py bdist_wheel for MarkupSafe: started
>   Running setup.py bdist_wheel for MarkupSafe: finished with status \'done\'
>   Stored in directory: /root/.cache/pip/wheels/88/a7/30/e39a54a87bcbe25308fa3ca64e8ddc75d9b3e5afa21ee32d57
> Successfully built itsdangerous MarkupSafe
> Installing collected packages: itsdangerous, click, Werkzeug, MarkupSafe, Jinja2, flask
> Successfully installed Jinja2-2.9.6 MarkupSafe-1.0 Werkzeug-0.12.2 click-6.7 flask-0.12.2 itsdangerous-0.24
>  ---> ce41f2517c16
> Removing intermediate container cac3222673a3
> Step 3/4 : CMD python app.py
>  ---> Running in 2197e5263eff
>  ---> 0ab91286958b
> Removing intermediate container 2197e5263eff
> Step 4/4 : COPY app.py /app.py
>  ---> f1b2781b3111
> Removing intermediate container b92b506ee093
> Successfully built f1b2781b3111
> Successfully tagged python-hello-world:latest
```

Verify that your image shows up in your image list via `docker image ls`.

```bash
docker image ls
```

```bash
> REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
> python-hello-world   latest              f1b2781b3111        26 seconds ago      99.3MB
> python               3.6.1-alpine        c86415c03c37        8 days ago          88.7MB
```

Notice that your base image, python:3.6.1-alpine, is also in your list.

