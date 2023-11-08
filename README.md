# mattermost-docker

# Table of Contents

1. [Introduction](#introduction)
2. [File Structure](#file-structure)
3. [Setup Instructions](#setup-instructions)
   - [1. Clone the Repository](#1-clone-the-repository)
   - [2. SSL Certificate Generation](#2-ssl-certificate-generation)
     - [2.1. Self-Signed SSL Certificate](#21-self-signed-ssl-certificate)
     - [2.2. Valid SSL Certificate](#22-valid-ssl-certificate)
   - [3. Domain Configuration](#3-domain-configuration)
   - [4. Starting the Service](#4-starting-the-service)
   - [5. Set Permissions](#5-set-permissions)
4. [Configuration Details](#configuration-details)
5. [Using the Mattermost Service](#using-the-mattermost-service)
6. [Troubleshooting](#troubleshooting)
7. [Contributing](#contributing)
8. [License](#license)

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

## File structure:
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

### 1. Clone the repository:
```bash
git clone https://github.com/benjisho/mattermost-docker.git
cd mattermost-docker
```

### 2. Generate SSL certificate into `${PWD}/volumes/web/cert/` directory

#### 2.1. Option 1 - Generate a self signed SSL certificate into `certs/` directory
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
### 3. Replace `yourdomain.com` with your Mattermost server FQDM in the following files:

        - `nginx/conf.d/mattermost.conf`

```bash
vi nginx/conf.d/mattermost.conf
# <or>
nano nginx/conf.d/mattermost.conf
```

        - `docker-compose.yml`

```bash
- MM_SERVICESETTINGS_SITEURL=https://yourdomain.com
```

### 4. Customize the Database password in the `docker-compose.yml` file.
```bash
- POSTGRES_PASSWORD=VeryStrongPassword123!
# <and also this line>
- MM_SQLSETTINGS_DATASOURCE=postgres://mmuser:VeryStrongPassword123!@postgres:5432/mattermost?sslmode=disable&connect_timeout=10
```
        > unfortunately environment variables do not work here.
### 5. Run the following command to start your service:
```bash
docker-compose up -d
```
### 6. Give permissions to the volumes.
```
chmod -R 777 ./volumes/app/mattermost/config
chmod -R 777 ./volumes/app/mattermost/logs
chmod -R 777 ./volumes/app/mattermost/data
```

## Contributing
Contributions are welcome! Please read the contributing guidelines before getting started.

## License
GNU License