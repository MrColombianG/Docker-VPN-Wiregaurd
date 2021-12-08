# Docker VPN: Setup Using Wireguard VPN Server 

This Repository will provide instructions on how to install Docker into your Virtual Machine (VM) and how to use Wireguard VPN Server within the Docker. I will be using Debian 64 Bit for this isntallation. 

##  Install Docker onto Virtual Machine


Run the following two commands in sudo or root:

    apt update
    apt install docker.io
    
The first command addresses updates needed from the apt repository. 

## Installing Docker Compose 

Before we begin installing the Docker, we need to install curl with this command

    apt install curl 
    
Curl is a tool to transfer data from one server to another. This is why we will be using it to install Docker Compose from Github.

The next command will isntall docker-compose
  
    curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose

After the docker-compose is done isntalling, run the following command to check if the version is the correct one.

    docker-compose -version
    
Version 1.29.2 should be the correct version.

## Creating Directories 

This following command will create two new directories and these will be used in the wireguard configuriation. 

    mkdir -p ~/wireguard/config
    
## Make the .yml file 

The yml file configures the option to deploy multiple dockers in the same container. This set up will allow the .yml file to host the wireguard VPN server.
The following command will find the ip address and you will need to know this before connecting to your host

    ip addr

The following command will install vim 

    apt install vim
    
The following command will create the docker-compose.yml file and allow sudo user to edit

    vim ~/wireguard/docker-compose.yml

Now copy and paste the text below into the file to configure it

    version: '3.8'
    services:
      wireguard:
        container_name: wireguard
        image: linuxserver/wireguard
        environment:
          - PUID=1000
          - PGID=1000
          - TZ=Asia/Hong_Kong
          - SERVERURL=1.2.3.4
          - SERVERPORT=51820
          - PEERS=pc1,pc2,phone1
          - PEERDNS=auto
          - INTERNAL_SUBNET=10.0.0.0
        ports:
          - 51820:51820/udp
        volumes:
          - type: bind
            source: ./config/
            target: /config/
          - type: bind
            source: /lib/modules
            target: /lib/modules
        restart: always
        cap_add:
          - NET_ADMIN
          - SYS_MODULE
        sysctls:
          - net.ipv4.conf.all.src_valid_mark=1

Before saving the file there are a few changes that need to be made:
  - Change the TZ (timezone) to a a time zone that is applicable to you
  - Change the SERVERURL to the ip address that you had earlier
  - Change the PEERS to different devices or users that would be using this VPN server
  - Now you can save the file by pressing esc and run the following command to view your file to see if the edits made it's change

    cat ~/wireguard/docker-compose.yml

## Creating the Wireguard VPN server

Use the following command to check to see if there is a wireguard directory and the second command to create the VPN docker container from the .yml file

    cd ~/wireguard/
    docker-compose up -d

After this you can now use digital ocean to create a droplet and connect it to your wiregaurd by copying the private key into the interface and this should allow you to start your wireguard VPN server.

## How to connect your phone to Wireguard VPN Server

- Step 1: Run this command
        docker-compose logs -f wireguard
- Step 2: Downlaod Wireguard app and open up on your phone
- Step 3: Click the + button and choose "Create from QR code" 
- Step 4: Scan the QR code in the terminal and chose the option designated for your phone

## How to connect your laptop to Wireguard VPN Server

- Step 1: Run this command
        cd ~/wiregaurd/config
- Step 2: Find the files with 'ls' and decide the PEER option needed for your device
- Step 3: Change the directories toe the PEER option selected
- Step 4: To see if the command is configured with the file then type this command
        ls
        cat peer_pc1.conf
        
- Step 5: Open up your Wireguard application and add an empty tunnel to connect the VM file location to the wireguard application outside the VM
- Step 6: Copy and paste the information from the conf file selected into the new tunnel, then click save

This is the end of the installation and now you may be using Wireguard VPN server for your devices.

        The proof of completion is above in 2 seperate files. 
        







  
    
    
  
