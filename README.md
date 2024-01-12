# mattermost-docker

## Table of Contents

1. [Introduction](#introduction)
2. [File Structure](#file-structure)
3. [Setup Instructions](#setup-instructions)
   - 3.1. [Clone the Repository](#1-clone-the-repository)
   - 3.2. [SSL Certificate Generation](#2-ssl-certificate-generation)
     - 3.2.1. [Self-Signed SSL Certificate](#21-option-1---self-signed-ssl-certificate)
     - 3.2.2. [Valid SSL Certificate](#22-option-2---generate-a-valid-certificate)
   - 3.3. [Domain Configuration](#3-domain-configuration)
   - 3.4. [Customize Parameters in `.env`](#4-customize-parameters-in-env)
     - 3.4.1. [Database Credentials](#customize-the-database-credentials)
     - 3.4.2. [Mattermost Resource Limits](#customizeuncomment-the-mattermost-resource-limits)
     - 3.4.3. [Mattermost Image and Tag](#customizeuncomment-the-mattermost)
   - 3.5. [Starting Mattermost Service](#5-starting-mattermost-service)
   - 3.6. [Set Permissions](#6-set-permissions)
4. [Contributing](#contributing)
5. [License](#license)

## Introduction

Welcome to the `mattermost-docker` project! This repository contains a Docker-based deployment setup for [Mattermost](https://mattermost.com/), an open-source, self-hosted Slack-alternative that brings team communication into one place, making it searchable and accessible anywhere.

Our aim is to provide a straightforward and containerized way to set up Mattermost, ensuring quick and easy deployment for teams looking for an enterprise-grade messaging platform. With Docker at its core, this project encapsulates best practices and lessons learned from deploying Mattermost in various environments.

Highlights of using this Docker setup for Mattermost include:

- **Simplicity:** Just a few steps to get up and running with a fully functional Mattermost server.
- **Flexibility:** Easy to customize, backup, and migrate your Mattermost instance.
- **Security:** Options to configure SSL/TLS to keep your communication secure.
- **Scalability:** Designed to scale with your organization’s needs, from small teams to large enterprises.

We encourage you to read through the documentation to understand how to configure and use the provided Docker images and to adapt them to your needs.

Let's get your Mattermost server rolling!

## File structure

```bash
/mattermost-docker
├── docker-compose.yml
├── .env
├── nginx
│   └── conf.d
│       └── mattermost.conf
└── volumes
    ├── app
    │   └── mattermost
    │       ├── config
    │       ├── data
    │       ├── logs
    │       ├── plugins
    │       ├── client
    │       │   └── plugins
    │       └── bleve-indexes
    ├── db
    │   └── (Postgres data files here)
    └── web
        └── cert
            ├── cert.crt  # Your SSL certificate
            └── key.key   # Your SSL certificate key

```

## Setup Instructions

### 1. Clone the repository

```bash
git clone https://github.com/benjisho/mattermost-docker.git
cd mattermost-docker
```

### 2. SSL Certificate Generation

Generate SSL certificate into `${PWD}/volumes/web/cert/` directory

#### 2.1. Option 1 - Self-Signed SSL Certificate

Option 1 - Generate a self signed SSL certificate into `certs/` directory
Run the following command to generate a 2048-bit RSA private key, which is used to decrypt traffic:

```bash
openssl genrsa -out ${PWD}/volumes/web/cert/key.key 2048
```

Run the following command to generate a certificate, using the private key from the previous step.

```bash
openssl req -new -key ${PWD}/volumes/web/cert/key.key -out ${PWD}/volumes/web/cert/cert.csr
```

Run the following command to self-sign the certificate with the private key, for a period of validity of 365 days:

```bash
openssl x509 -req -days 365 -in ${PWD}/volumes/web/cert/cert.csr -signkey ${PWD}/volumes/web/cert/key.key -out ${PWD}/volumes/web/cert/cert.crt
```

#### 2.2. Option 2 - Generate a valid certificate

```bash
scripts/issue-certificate.sh -d <your.domain.name.com> -o ${PWD}/volumes/web/cert/
```

### 3. Domain Configuration

Replace `yourdomain.com` with your Mattermost server FQDM in the following files:

- In file `nginx/conf.d/mattermost.conf`

```bash
vi nginx/conf.d/mattermost.conf
# <or>
nano nginx/conf.d/mattermost.conf
```

- In file `.env`

```bash
DOMAIN=mm.example.com
```

### 4. Customize parameters in `.env`

#### Customize the Database Credentials

Customize the Postgres database credentials in the `.env` file.

```bash
POSTGRES_USER=mmuser
POSTGRES_PASSWORD=mmuser_password
POSTGRES_DB=mattermost
```

#### Customize/Uncomment the Mattermost Resource Limits

```bash
MATTERMOST_CPU_LIMIT=2
MATTERMOST_MEMORY_LIMIT=4g
```

> Minimum recommended configuration options for Mattermost 250-500 users are `2 cpu` and `4g memory`
> Minimum recommended configuration options for Mattermost 500-1000 users are `2 cpu` and `8g memory`
> Minimum recommended configuration options for Mattermost 1000-2000 users are `4 cpu` and `16g memory`
> Minimum recommended configuration options for Mattermost 2000-4000 users are `8 cpu` and `32g memory`

#### Customize/Uncomment the Mattermost

```bash
MATTERMOST_IMAGE=mattermost-enterprise-edition
MATTERMOST_IMAGE_TAG=9.1.2
```

> For personal use, you can use `image: mattermost/mattermost-team-edition`
> `MATTERMOST_IMAGE=mattermost/mattermost-team-edition`
> For Enterprise Edition use `image: mattermost/mattermost-enterprise-edition`

### 5. Starting Mattermost Service

Run the following command to start your service:

```bash
docker-compose up -d
```

### 6. Set Permissions

Give permissions to the volumes.

```
chmod -R 777 ./volumes/app/mattermost/config
chmod -R 777 ./volumes/app/mattermost/logs
chmod -R 777 ./volumes/app/mattermost/data
```

## Contributing

Contributions are welcome! Please read the contributing guidelines before getting started.

- Page is based on information provided in the [Official Mattermost Docker Repository](https://github.com/mattermost/docker)

## License

GNU License
