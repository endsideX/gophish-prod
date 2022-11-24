# GoPhish for Production

## Features
- [Docker](https://www.docker.com/)
- [Nginx](https://www.nginx.com/)
- [Let's Encrypt](https://letsencrypt.org/)
- [MySQL Database](https://www.mysql.com/)
- [Postfix](http://www.postfix.org/)
- [Adminer database management UI](https://www.adminer.org/)
- Tested to launch on an Ubuntu 18.04 LTS Server

## Requirements
* For local setup, Get the right flavor of Docker for your OS...
    - [Docker for Mac](https://docs.docker.com/docker-for-mac/install/)
    - [Docker for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
    - [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)

    **Note:** The recommended requirement for deployment of this project is 4 GB RAM.
    For Docker for Mac, this can be set by following these steps:

    Open Docker > Preferences > Advanced tab, then set memory to 4.0 GiB

* A registered domain name


## Setup on a Production Server

### Initial Setup
- Clone this repo
- Update your `etc/env.dist` and `/etc/gophish/config.json` file to a more secure database password
- Create your env file in to the root directory
    - `cp etc/env.dist .env`
- Change the `server_name` in `etc/nginx/config.conf` from `127.0.0.1` to **your domain name**

- Install **Make**
    - `sudo apt install make`

### Run GoPhish on a Production Server
- Install and update required packages
    - `make init`
- Follow on-screen prompts
- Restart Server in order for changes to take effect
- Build containers
    - `cd gophish-prod`
    - `make build`
- Run GoPhish
    - `make up`
- Get the Gophish Password
    - `docker logs gophish_prod`
    -  Find part "Please login with the username admin and the password ####"

#### Generate SSL Certificates using Let's Encrypt
- Shell into the Nginx container:
    - `docker exec -it gophish-proxy bash`
- Run Certbot:
    - `certbot --nginx`
- update the configuration stored in the `/etc/nginx/conf.d/`
    - update `listen 3334;` to `listen 3334 ssl;` and copy & paste the rest from the first server block (`ssl_certificate` to `ssl_dhparam`).
- Follow on-screen prompts
- Restart Nginx service
    - `service nginx restart`
    - **Note:** the shell will close because this command restarts the container
- Restart the Nginx container
    - `make up`
- Visit your domain to access the GoPhish admin UI
    - GoPhish admin UI is running on `server_name:3334`
    - Phishing server is running on `server_name:80` or `server_name`
