# mattermost-docker

- [Introduction](#introduction)
- [File Structure](#file-structure)
- [Setup Instructions](#setup-instructions)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. SSL Certificate Generation](#2-ssl-certificate-generation)
    - [2.1. Self-Signed SSL Certificate](#21-self-signed-ssl-certificate)
    - [2.2. Valid SSL Certificate](#22-valid-ssl-certificate)
  - [3. Domain Configuration](#3-domain-configuration)
  - [4. Starting the Service](#4-starting-the-service)
  - [5. Set Permissions](#5-set-permissions)
- [Configuration Details](#configuration-details)
- [Using the Mattermost Service](#using-the-mattermost-service)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)
- [Contact Information](#contact-information)

## File structure:
```bash
/mattermost-docker
├── docker-compose.yml
├── .env
├── nginx
│   └── conf.d
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

3. Replace `yourdomain.com` with your Mattermost server FQDM in the following file:
```
nginx/conf.d/mattermost.conf
```
4. Runthe following command to start your service:
```bash
docker-compose up -d
```

5. Give permissions
```
chmod -R 777 ./volumes/app/mattermost/config
chmod -R 777 ./volumes/app/mattermost/logs
chmod -R 777 ./volumes/app/mattermost/data
```