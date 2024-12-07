
# Deploying OrangeHRM with Docker on Ubuntu

This project demonstrates deploying **OrangeHRM**, an open-source human resource management system, using **Docker** and **Ubuntu**. 

---

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Installing Docker, Docker-Compose, and NGINX Proxy Manager](#installing-docker-docker-compose-and-nginx-proxy-manager)
4. [Setting up OrangeHRM](#setting-up-orangehrm)
5. [Running the Installation Wizard](#running-the-installation-wizard)
6. [Accessing OrangeHRM](#accessing-orangehrm)
7. [Video](#video)
8. [Acknowledgements](#acknowledgements)


---

## Introduction

**OrangeHRM** is an open-source HR management tool ideal for businesses ranging from startups to medium-sized enterprises. OrangeHRM enables the automation of all key HR activities, allowing teams to focus on people instead of repetitive administrative tasks. It offers features like:
- Personnel management
- Recruitment tools
- Leave management
- Time tracking by project
- LDAP authentication support

### Why Use Docker for OrangeHRM?

Deploying OrangeHRM using Docker offers several advantages:
1. **Ease of Deployment**: Docker simplifies the setup by packaging OrangeHRM and its dependencies (e.g., MariaDB) into containers.
2. **Isolation**: Each service (web server, database, etc.) runs in its own container, reducing conflicts and ensuring a clean environment.
3. **Scalability**: Add resources or scale services independently without disrupting operations.
4. **Portability**: Run OrangeHRM on any system that supports Docker, making it highly portable across different environments.
5. **Efficiency**: Docker images are lightweight and allow for faster deployments compared to traditional virtual machines.
6. **Simplified Maintenance**: Manage updates and dependencies more effectively by simply replacing or updating individual containers.

Docker empowers organizations to deploy OrangeHRM with minimal manual configuration, saving time and ensuring reliability.

---

## Prerequisites

Ensure you have the following:
- A system or server to host the web application
- Docker and Docker-Compose installed
- **Optional**: A reverse proxy (e.g., NGINX Proxy Manager) for internet access
- **Optional**: A domain or subdomain for DNS records

---

## Installing Docker, Docker-Compose, and NGINX Proxy Manager

Use the following script to quickly install Docker-CE, Docker-Compose, Portainer-CE, and NGINX Proxy Manager:

```bash
wget https://gitlab.com/bmcgonag/docker_installs/-/raw/main/install_docker_nproxyman.sh
ls -lh
sudo chmod +x ./install_docker_nproxyman.sh
./install_docker_nproxyman.sh
```
Note: If Docker-Compose does not get installed then reinstall it again by using

```bash
sudo apt install docker-compose
```


---

## Setting up OrangeHRM

1. **Create a directory for OrangeHRM**:
   ```bash
   sudo mkdir -p docker/orangehrm
   cd docker/orangehrm
   ```

2. **Create a `docker-compose.yml` file**:
   ```bash
   sudo nano docker-compose.yml
   ```

3. **Add the following YAML configuration**:
   ```yaml
   version: "3.3"
   services:
     orangehrm:
       image: orangehrm/orangehrm:latest
       restart: unless-stopped
       ports:
         - "8380:80"
         - "8343:443"
       environment:
         - ORANGEHRM_DATABASE_HOST=mariadb
         - ORANGEHRM_DATABASE_USER=<user>
         - ORANGEHRM_DATABASE_PASSWORD=<a StrongPassword>
         - ORANGEHRM_DATABASE_NAME=orangehrm
         - PUID=998
         - PGID=100
       volumes:
         - ./orange:/orangehrm
       depends_on:
         - mariadb
       links:
         - mariadb:mariadb
      
     mariadb:
       image: mariadb:10.2
       restart: unless-stopped
       volumes:
         - ./db_orange:/var/lib/mysql
       environment:
         - MYSQL_ROOT_PASSWORD=<aStrongPassword>
         - MYSQL_ROOT_USER=<user>
         - MYSQL_DATABASE=orangehrm
   ```

   Replace `<user>` and `<aStrongPassword>` with your credentials.

4. **Start the containers**:
   ```bash
   sudo docker-compose up -d
   ```

5. **Finding IP address**:
   Open the terminal and find the IP address of the virtual machine/server, which has docker-CE and docker-compose installed on:
   ```bash
   ip a
   ```

---

## Running the Installation Wizard

1. Open a browser and navigate to `http://<server-ip>:8380`.
2. Follow the on-screen instructions:
   - Select **"Fresh Installation"** (for the first time).
   - Agree to the GNU V2 License.
   - For Database configuration, use the following database credentials:
     - **Database Host Name**: `orangehrm_mariadb_1` (can be found using ```bash sudo docker-compose ps```)
     - **DB Name**: `orangehrm`
     - **User**: `root`
     - **Password**: (docker-compose password)
   - Set up the admin user and base organisation details.

3. Complete the installation wizard.

---

## Accessing OrangeHRM

Once the installation wizard completes:
1. Navigate to the OrangeHRM login screen.
2. Log in with the admin credentials created during installation.
3. Configure the system to suit your needs.

---

## Video



https://github.com/user-attachments/assets/471fa372-8f7c-4f79-8947-29942cc7287b



---


## Acknowledgements

I would like to express my sincere gratitude to the following individuals for their support and resources during this project:

- **Prof. Ashok Harnal**: For his unwavering support, guidance, and for providing me the opportunity to learn something new.
- **Dan Calloway (Datapioneer)**: For his insightful YouTube video on deploying OrangeHRM.
- **Brian McGonagill**: For his invaluable source codes and documentation that were referred to in this project.


---

