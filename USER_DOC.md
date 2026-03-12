# User Documentation

This document explains how to run and use the Inception WordPress infrastructure.

---

# Requirements

Before running the project it is recommended you have the following installed:

- sudo
- ssh
- vim
- neovim
- Docker / Docker Compose
- Make
- GUI
- A modern web browser

You must also be able to modify your `/etc/hosts` file.

## Installations

### 1. Install sudo
```
vi /etc/apk/repositories
```
- When it opens type i and then delete "#" infront of last line
- Now click esc, then ":wq" to save and exit.
```
su -
apk update
apk add sudo
sudo visudo
```
When it opens click "i" and uncomment (delete "#" infront of) %sudo ALL=(ALL:ALL) ALL. Then exit

After this check if group sudo exist with the command: getent group sudo. If no output is given it means that not. So we have to create the group and after add our user in the group: "addgroup sudo", "adduser rhvidste sudo". Where rhvidste is my username and sudo is the group. Now you are good to go to exercise sudo writes with your user.

### 2. Install ssh

```
sudo apk update
sudo apk add nano
sudo nano /etc/ssh/sshd_config
```
now when it opens uncomment Port and change it into 4241. To save it do CTRL+O and enter. To exit nano do CTRL+X.
```
sudo vi /etc/ssh/ssh_config
```
Then uncomment the Post and change it into 4241. Now save and exit.
```
sudo rc-service sshd restart
```
To check if it is listening from 4241 we can see with this command:
```
netstat -tuln | grep 4241
```
Now go back to VM. Settings. Network. Advanced. Port Forwarding. Click green + and in both ports type "4241". Click OK 2 times.

Open normal terminal to check if it works. type:
```
ssh localhost -p 4241
yes
userpass
```
then
```
sudo apk add xorg-server xfce4 xfce4-terminal lightdm lightdm-gtk-greeter xf86-input-libinput elogind
sudo setup-devd udev
sudo rc-update add elogind
sudo rc-update add lightdm
sudo reboot
```

now go back to VM and it will show a little window for log in with your name :D Log in there and you will see a nice background with a little blue mouse in the middle :D Now if you close it and start again in VB, it will show this again.

### 3. vim
#### 1. Update the package index
```
apk update
```
#### 2. Install vim
```
apk add vim
```
This installs the standard Vim package.

#### 3. Verify installation
```
vim --version
```
You should see something like:
```
VIM - Vi IMproved 9.x
```
#### 4. If you get “permission denied”
You need root privileges.

Either switch to root:
```
su
```
or run:
```
sudo apk add vim
```
### 4. neovim

#### 1. Install Required Dependencies
- LazyVim requires:
- neovim
- git
- nodejs
- npm
- ripgrep
- fd
- gcc / build tools (for some plugins)

Let's install them:
```
apk update
apk add neovim git nodejs npm ripgrep fd build-base curl
```
Optional but useful:
```
apk add lazygit
```
#### 2. Install LazyVim Starter Config
```
git clone https://github.com/LazyVim/starter ~/.config/nvim
```
Then remove the git history:
```
rm -rf ~/.config/nvim/.git
```
This leaves you with a clean config.

#### 3. Start Neovim

Run:
```
nvim
```
LazyVim will automatically:
- install the plugin manager lazy.nvim
- install all plugins
- build dependencies

You will see plugins downloading.

### 5. Docker / Docker Compose

- First connect using ssh at terminal: ssh localhost -p 4241
- Update Alpine:
```
  sudo apk update && sudo apk upgrade
```
- then type:
```
sudo vi /etc/apk/repositories
```
- and uncomment first line and save and close.
- install Docker and Docker Compose:
```
sudo apk add docker docker-compose
```
- run:
```
sudo apk add --update docker openrc
```
- To start the Docker daemon at boot, run:
```
sudo rc-update add docker boot
```
- to ensure the status is running:
```
service docker status
```
- If it is stoped type:
```
sudo service docker start
```
- and check again:
```
service docker status
```
Connecting to the Docker daemon through its socket requires you to add yourself to the docker group:
```
sudo addgroup rhvidste docker
```
- Installing Docker Compose:
```
sudo apk add docker-cli-compose
```
### 6. Make
#### 1. Install Make
- Run:
```
apk add make
```
#### 2. If you get a permission error
- You need root privileges.
- Use either:
```
sudo apk add make
```
- or switch to root:
```
su
apk add make
```
#### 3. Verify Installation
```
make --version
```
- You should see something like:
```
GNU Make 4.x
```

#### 4. Recommended (better for development)
Most developers install the build tools package which includes make, gcc, and other compilation tools:
```
apk add build-base
```
This installs:

- make
- gcc
- g++
- musl-dev
- other build utilities

Very useful for compiling programs or building Neovim plugins.

### 7. GUI
#### 1. Install Xorg (the display server)
First install the graphical system:
```bash
apk update
apk add xorg-server xf86-video-vesa xf86-input-libinput
```
These provide:
- Xorg display server
- generic video driver
- keyboard/mouse input
#### 2. Install a lightweight desktop environment
For Alpine, a very good choice is XFCE.

Install it with:
```
apk add xfce4 xfce4-terminal
```

#### 3. Install a login manager (optional but recommended)
This gives you a graphical login screen.
```
apk add lightdm lightdm-gtk-greeter
```
Enable it:
```
rc-update add lightdm
rc-service lightdm start
```
#### 4. Enable DBus (required for desktops)
```
apk add dbus
rc-update add dbus
rc-service dbus start
```
#### 5. Start the GUI manually (if not using LightDM)
```
startx
```
#### 6. Install a web browser
Example:
```
apk add firefox
```
or a lighter browser:
```
apk add chromium
```
#### 7. Reboot
```
reboot
```
You should now boot into the graphical desktop.
if you need to boot it from the terminal just type:
```
startx
```

# Project Setup

Clone the repository:

```bash
git clone <repository_url>
cd inception

## Configure Environment Variables
The project uses an .env file located in:
```bash
srcs/.env
```
This file contains configuration values such as:
- database credentials
- WordPress admin account
- domain name
Example configuration:
```
DOMAIN_NAME=rhvidste.42.fr

MYSQL_DATABASE=wordpress_db
MYSQL_USER=rhvidste
MYSQL_PASSWORD=********

WORDPRESS_ADMIN=ross
WORDPRESS_ADMIN_PASSWORD=********
WORDPRESS_ADMIN_EMAIL=admin@rhvidste.42.fr
```

## Configure Domain Name
get the ip by typing:
```
ip a
```
then look for something like 
```
inet 127.0.0.1/8 scope host lo
```

Add the following entry to your **/etc/hosts** file:
```
sudo nano /etc/hosts
```

Add this line (replace the ip with the one you found using Ip a):
```
127.0.0.1 rhvidste.42.fr
```

## Build and Start the Infrastructure
Run:
```
make
```
This command will:

- Create persistent data directories
- Build Docker images
- Start the containers

The services will run in the background.

## Access the Website
Open your browser and go to:
( this will reflect what is in your .env variables under domain name)
```
https://rhvidste.42.fr
```
You may see a security warning due to the self-signed certificate.

Accept the certificate to continue.

## WordPress Login
The WordPress admin panel is available at:
```
https://rhvidste.42.fr/wp-admin
```
Login using the credentials defined in the .env file.

## Stopping the Infrastructure
To stop all containers:
```
make down
```
## Cleaning the Project
To remove containers, images, and volumes:
```
make clean
```
To completely reset the project:
```
make fclean
```
or to clean and reboot:
```
make re
```
## Persistent Data
The project stores data in the following directories:
```
/home/<login>/data/mariadb
/home/<login>/data/wordpress
```

## To SSH into the host VIM
```
root:
ssh root@192.168.64.2 -p 4241

user:
ssh rhvidste@192.168.64.2 -p 4241
```

## to change the port manually
### Method 1. ( partial change )
#### 1.
- Inside the docker-compose.yml 
- Change the nginx port
From:
```
443:443
```
To:
```
8443:443
```
#### 2.
```
docker exec -it wordpress sh
cd /var/www/html
wp option update siteurl 'https://rhvidste.42.fr:8443' --allow-root
wp option update home 'https://rhvidste.42.fr:8443' --allow-root
exit
```
### Method 2. ( Full change )
#### 1.
Change the docker-compose.yml port to:
```
8443:8443
```
Then in nginx.conf have:
```
server {
	listen 8443 ssl;
	listen [::]:8443 ssl;
	server_name rhvidste.42.fr;
```








