# Run Your Private Docker Registry on Your Own Server

We all want to have a deployment system where we can deploy our services without any hussle.
Here comes containerization, there are a lot of private docker registry giving you fully managed service.
But if you are looking for a your own then this article is for you.

### Why Set Up a Private Docker Registry?

There could be a lot of reason that we can say. But for me I set up the Private Registry because I don't want to pay subscription for a service that I need to use for

### What we need to start

- A server with Docker and Docker Compose installed.
- Access to an S3-compatible storage service for storing image data.
- Basic understanding of Docker and AWS S3.

### Security

we will Secure Your Registry with Basic Authentication to make it simple

```
sudo apt-get install apache2-utils
mkdir auth
htpasswd -Bbn yourusername yourpassword > auth/registry.password
```

Replace yourusername and yourpassword with your desired credentials.

### Set Up Your Docker Compose File

Before Docker Compose file we need to make sure we have .env file.
here is a example file for .env

```env
REGISTRY_STORAGE_S3_REGION=sgp1
REGISTRY_STORAGE_S3_BUCKET=docker-registry-bucket
REGISTRY_STORAGE_S3_ROOTDIRECTORY=/docker-registry
REGISTRY_STORAGE_S3_REGIONENDPOINT=https://s3.Region.amazonaws.com
REGISTRY_STORAGE_S3_ACCESSKEY=
REGISTRY_STORAGE_S3_SECRETKEY=
REGISTRY_AUTH_HTPASSWD_PATH=./auth/registry.password
REGISTRY_PORT=5000
```

Create a docker-compose.yml file with the following content, replacing placeholder values with your actual S3 and authentication details:

#### docker-compose.yml

```yml
version: '3.7'

services:
  registry:
    image: registry:2
    restart: always
    ports:
      - '127.0.0.1:${REGISTRY_PORT}:5000'
    environment:
      REGISTRY_STORAGE: s3
      REGISTRY_STORAGE_S3_REGION: ${REGISTRY_STORAGE_S3_REGION}
      REGISTRY_STORAGE_S3_BUCKET: ${REGISTRY_STORAGE_S3_BUCKET}
      REGISTRY_STORAGE_S3_ROOTDIRECTORY: ${REGISTRY_STORAGE_S3_ROOTDIRECTORY}
      REGISTRY_STORAGE_S3_REGIONENDPOINT: ${REGISTRY_STORAGE_S3_REGIONENDPOINT}
      REGISTRY_STORAGE_S3_ACCESSKEY: ${REGISTRY_STORAGE_S3_ACCESSKEY}
      REGISTRY_STORAGE_S3_SECRETKEY: ${REGISTRY_STORAGE_S3_SECRETKEY}
      REGISTRY_STORAGE_S3_CHUNKSIZE: 5242880
      REGISTRY_STORAGE_S3_ENCRYPT: 'true'
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
    volumes:
      - ./auth:/auth
```

### Run Your Registry

With your docker-compose.yml and .env files configured, start your registry by running:

```bash
docker-compose up -d
```

Your private Docker registry is now running on your server, accessible locally at the port specified in REGISTRY_PORT, securely storing data in your S3 bucket, and protected by basic authentication.
