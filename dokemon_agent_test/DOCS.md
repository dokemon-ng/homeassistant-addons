# Dockemon Agent 🐳👾

![Supports arm64 Architecture][arm64-shield] 
![Supports amd64 Architecture][amd64-shield] 
![Supports armv7 Architecture][armv7-shield]
[![MIT License][mit]][mit-url]
[![Add repository to your Home Assistant][repository-badge]][repository-url]

<div align="center">
  <img alt="Dockemon Logo" src="https://raw.githubusercontent.com/dokemon-ng/.github/main/dokemon-logo.png" width="500">
  <p><em>Warning: This is currently in testing phase - not recommended for production use</em></p>
</div>

## About Dockemon

Dockemon is a friendly GUI for managing Docker containers across multiple servers from a single interface. Designed with simplicity in mind, it provides an intuitive way to manage your containerized environments.

## 🚧 Important Notice
This agent is currently in **testing phase**. Please:
- Do not use in production environments
- Expect breaking changes
- Report any issues you encounter

## Supported Architectures
- arm64
- amd64
- armv7

[arm64-shield]: https://img.shields.io/badge/arm64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
[repository-badge]: https://img.shields.io/badge/Add%20repository%20to%20my-Home%20Assistant-41BDF5?logo=home-assistant&style=for-the-badge
[repository-url]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fdokemon-ng%2Fhomeassistant-addons
[mit]: https://img.shields.io/badge/MIT-green?style=for-the-badge

<div align="center">
  <img alt="Dokémon (Dokemon) Logo" src="https://raw.githubusercontent.com/dokemon-ng/.github/refs/heads/main/dokemon-logo.png" width="500">
</div>

## About

Dokémon (Dokemon) is a friendly GUI for managing Docker Containers. You can manage multiple servers from a single Dokémon (Dokemon) instance.

## Quickstart

You can run the below commands to quickly try out Dokémon (Dokemon).

    # Create directory to store Dokémon (Dokemon) data
    sudo mkdir /dokemondata

    # Run Dokemon
    sudo docker run -p 9090:9090 \
      -v /dokemondata:/data \
      -v /var/run/docker.sock:/var/run/docker.sock \
      --restart unless-stopped \
      --name dokemon-server -d javastraat/dokemon-server:latest

**Note:** Whenever possible, it is recommended that you run Dokémon (Dokemon) in a private network and do not expose it to the Internet. In cases where this is not possible, for example when running on a VPS to which you only have public access, you should run Dokémon (Dokemon) behind an SSL enabled reverse proxy and use a strong password for maximum security. Refer the next section for sample configuration using Traefik.

## Using Traefik with LetsEncrypt SSL certificate

This is an example configuration for running Dokémon (Dokemon) behind Traefik with LetsEncrypt SSL certificate.

**Note:** This is a sample configuration. Please modify it as per your requirements.

    version: "3.3"

    services:
      traefik:
        image: "traefik:v2.10"
        container_name: "traefik"
        command:
          - "--log.level=DEBUG"
          - "--accesslog=true"
          - "--api.insecure=true"
          - "--providers.docker=true"
          - "--providers.docker.exposedbydefault=false"
          - "--entrypoints.websecure.address=:443"
          - "--certificatesresolvers.dokemon.acme.tlschallenge=true"
          - "--certificatesresolvers.dokemon.acme.email=your.email@example.com"
          - "--certificatesresolvers.dokemon.acme.storage=/letsencrypt/dokemon.json"
        ports:
          - "443:443"
          - "8080:8080"
        volumes:
          - "./letsencrypt:/letsencrypt"
          - "/var/run/docker.sock:/var/run/docker.sock:ro"

      dokemon:
        image: javastraat/dokemon-server:latest
        container_name: dokemon-server
        restart: unless-stopped
        labels:
          - "traefik.enable=true"
          - "traefik.http.routers.dokemon.rule=Host(`dokemon.example.com`)"
          - "traefik.http.routers.dokemon.entrypoints=websecure"
          - "traefik.http.routers.dokemon.tls.certresolver=dokemon"
        ports:
          - 9090:9090
        volumes:
          - /dokemondata:/data
          - /var/run/docker.sock:/var/run/docker.sock

In the DNS settings for your domain, add an A record for the _Host_ which you have mentioned in the above config. The A record should point to the public IP address of your virtual machine.

1. Create a file named `compose.yaml` on your server. Copy and paste the above YAML definition into the file. Modify the email and host. Make any other changes as per your requirements.
2. Run `mkdir ./letsencrypt && mkdir /dokemondata`
3. Run `docker compose up -d`

Open https://dokemon.example.com (substitute your URL here which you entered as Host in the compose.yaml file) in the browser. It can take a few seconds for the SSL certificate to be provisioned. If you get an error related to SSL, please wait for a few moments and then refresh your browser.

## Screenshots

### Manage Multiple Servers
![Alt text](https://github.com/dokemon-ng/dokemon/raw/main/screenshots/screenshot-dokemon-nodes.jpg?raw=true)

### Manage Variables for Different Environments

![Alt text](https://github.com/dokemon-ng/dokemon/raw/main/screenshots/screenshot-dokemon-variables.jpg?raw=true)

### Deploy Compose Projects

![Alt text](https://github.com/dokemon-ng/dokemon/raw/main/screenshots/screenshot-dokemon-compose-up.jpg?raw=true)

### Manage Containers, Images, Volumes, Networks

![Alt text](https://github.com/dokemon-ng/dokemon/raw/main/screenshots/screenshot-dokemon-containers.jpg?raw=true)

## License

This project is [MIT Licensed](../LICENSE).
