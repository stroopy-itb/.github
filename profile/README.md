# Stroopy

## How to Deploy the Project

There are many ways to deploy the apps infrastructure. This guide specifically will deploy the apps infrastructure with this setup:
- Running on a Virtual Private Server (VPS)
- Apps and database running on a Docker container environment, setup using Docker Compose 
- Public access to the app is through a domain with SSL enabled

### Requirements
- A Virtual Private Server (VPS), this guide use IDCloudhost's Virtual Machine Resource running Rocky Linux version 8.4
- A Domain
- Deployed Backend Server and Deployed Frontent Client Docker Image. Please refer to the guide in both [server project](https://github.com/stroopy-itb/stroopy-server/blob/master/README.md) and [client project](https://github.com/stroopy-itb/stroopy-client/blob/master/README.md) readme on how to deploy the image

### Step-by-step Guide
- Connect to the VPS using ssh, open a terminal and run,
    ```
    ssh <your username>@<your vps public address>
    ```
- Setup the VPS with Docker
    - Install Docker, refer to [this guide](https://docs.docker.com/engine/install/rhel/). 
    
        change 
        ```
        https://download.docker.com/linux/rhel/docker-ce.repo
        ``` 
        to 
        ```
        https://download.docker.com/linux/centos/docker-ce.repo
        ```
  - check if Docker and Docker Compose already installed
    ```bash
    docker version
    docker compose version
    ```
- To organise the project better on the VPS filesystem create a directory named `stroopy` and move inside it
  ```bash
  mkdir stroopy
  cd stroopy
  ```

- Create `docker-compose.yml` file
  ```bash
  touch docker-compose.yml
  ```

- Example for `docker-compose.yml` content can be found in the server project [`docker-compose.production.yml` file](https://github.com/stroopy-itb/stroopy-server/blob/master/docker-compose.production.yml). Change the `server` and `client` image with deployed Docker Image name.

- create `.env` file this will be used for Database and Backend Server configuration
  ```bash
  touch .env
  ```

- Example for `.env` content can be found in the server project [`.env` file](https://github.com/stroopy-itb/stroopy-server/blob/master/.env).

- Start the docker compose
    ```bash
    docker compose up -d
    ```

- Open `<your vps public address>:81` on your browser to open Nginx Proxy Manager for Domain routing and SSL setup

- Login with these default credentials, change it to an actually secured credentials later.
    - email: admin@example.com
    - password: changeme

- Navigate to Hosts > Proxy Hosts

- Add Proxy Host for Backend Server
    - Click Add Proxy Host
    - Fill 'Domain Names' with domain name that you use for Backend Server app
    - Fill 'Forward Hostname / IP' with resource name that is defined in the `docker-compose.yml` file, in this case `server`
    - Fill 'Forward Port' with `API_PORT` defined in the `.env` file, in this case `3333`
    - Enable 'Block Common Exploit'
    - Move to SSL tab
    - Choose 'Request a New SSL Certificate'
    - Enable 'Force SSL', 'Http/2 Support' and 'HSTS Enabled'
    - Fill 'Email Address for Let's Encrypt' with an actual active email that you use, and agree to the Terms of Service
    - Save

- Add Proxy Host for Backend Server
    - Click Add Proxy Host
    - Fill 'Domain Names' with domain name that you use for Frontend Client app
    - Fill 'Forward Hostname / IP' with resource name that is defined in the `docker-compose.yml` file, in this case `client`
    - Fill 'Forward Port' with `80`
    - Enable 'Block Common Exploit'
    - Move to SSL tab
    - Choose 'Request a New SSL Certificate'
    - Enable 'Force SSL', 'Http/2 Support' and 'HSTS Enabled'
    - Fill 'Email Address for Let's Encrypt' with an actual active email that you use, and agree to the Terms of Service
    - Save

- Check if all works by visiting the app domain, you might want to add default Admin user, or modify a newly registered user role, directly through the database to have an Admin account for the app.

### Additional Resources
- Official Docker Compose Documentation
    > https://docs.docker.com/compose/
- Nginx Proxy Manager Documentation
    > https://nginxproxymanager.com