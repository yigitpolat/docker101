# Push to IBM Cloud Container Registry

This section describes how you can create an IBM Cloud Account and how to create an external private container registry for this workshop.

## Creating an IBM Cloud Account

If you didn't create your IBM Cloud account before the workshop, follow [IBM Cloud Registration Guide](https://app.gitbook.com/@volaka/s/ibm-cloud-registration/) to create it. Otherwise, you can skip to the next section.

## Create An External Private Container Registry

In this section, we will create a private container registry to deploy our built container images.

1. Run ibmcloud-devtools container which includes all necessary dev tools to access and manage IBM Cloud through terminal.

   ```bash
    docker run \
    -t \
    --detach \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v $PWD:/root/userpath \
    --name ibmcloud-devtools \
    volaka/ibmcloud-devtools

    docker exec -it ibmcloud-devtools ash
   ```

2. Login to IBM Cloud with your credentials.

   ```bash
    ibmcloud login
   ```

3. Login to IBM Cloud Container Registry server

   ```bash
    ibmcloud cr login
   ```

   Example output:

   ```bash
    Logging in to 'registry.eu-de.bluemix.net'...
    Logged in to 'registry.eu-de.bluemix.net'.

    Logging in to 'de.icr.io'...
    Logged in to 'de.icr.io'.

    OK
   ```

4. Create your variables for later use.
   * ICR\_URL is the URL that you saw when you login to Container Registry server.
   * ICR\_NS is the namespace that we are going to create to push our images. It should be **unique** globally.

     ```bash
     export ICR_URL=de.icr.io
     export ICR_NS=volaka-akbank-tutorial
     ```
5. Create namespace

   ```bash
    ibmcloud cr namespace-add $ICR_NS
   ```

6. There are two ways to create and push our image:
   1. ```bash
      docker build -t $ICR_URL/$ICR_NS/python-hello-world:latest . 
      docker push $ICR_URL/$ICR_NS/python-hello-world:latest
      ```
   2. ```bash
      ibmcloud cr build -t $ICR_URL/$ICR_NS/python-hello-world:latest .
      ```

      > Second option builds the image locally and directly pushes it.

