# Infrastructure notes

## Table of contents

1. [VPS - configuration](#1-vps---configuration)
2. [Nginx - installation](#2-nginx---installation)
3. [Nginx - adding new websites](#3-nginx---adding-new-websites)
4. [VPS - firewall, auto-updates](#4-vps---firewall-auto-updates)
5. [Adding SSL certificate](#5-adding-ssl-certificate)
6. [Configuring NodeJS process manager for your app](#6-configuring-nodejs-process-manager-for-your-app)

## 1. VPS - configuration

For new _blank_ server you need to set it up properly:

1. Update the server

   ```bash
   apt update
   apt upgrade
   ```

2. Restart the server

   ```bash
   shutdown now -r
   ```

   NOTE: Remember about the `-r` flag. Otherwise yoy won't be able to ssh into your server. In case of emergency your VPS provider should allow you to remotely start the server using the GUI.

3. Add new user with superuser previlages

   ```bash
   adduser <username>
   usermod -aG sudo <username>
   ```

   To switch to the new user execute:

   ```bash
   su <username>
   ```

   In order to ssh into your server with your user you need to store your **public ssh key** on the server. Login as your user, create a new file `~/.ssh/authorized_keys` and paste your public key. After all you will be able to ssh into your server.

   While connecting into the server just replace the `root` with your username like so:

   ```bash
   ssh <username>@<server_ip_address>
   ```

   Also you need to change access permissions to the file you've just created. It's mandatory since ssh daemon will complain. Execute such command:

   ```bash
   chmod 644 ~/.ssh/authorized_keys
   ```

4. Disable root login

   Having root login enabled causes serious security vulnerability. Moreover uncontrolled nor accidential root actions may permanently damage or even destroy your file system and operating system. It always should be disabled. To do so edit the ssh daemon config. Open the `/etc/ssh/sshd_config` config file and search such line:

   > PermitRootLogin yes

   Disable it by changing _yes_ phrase to _no_.

   NOTE: Please be careful when editing this config file. Any undesired changed my damage your ssh connection leading to damage the server since it's the **only** way to connect to it.

   Save the file and restart the daemon by executing:

   ```bash
   sudo service sshd restart
   ```

   NOTE: Please ensure you have access to your user account you've created earlier. If not, you will permanently lose privilages to connect to the server making it unusable. If your ssh connection got closed while restarting the ssh daemon service - something went wrong.

## 2. Nginx - installation

1. To install Nginx execute:

   ```bash
   sudo apt install nginx
   ```

2. To start Nginx service execute:

   ```bash
   sudo service nginx start
   ```

3. Test your Nginx installation.

   To test your nginx installation open your browser and visit your VPS IP address. You should see the Nginx welcome message.

## 3. Nginx - adding new websites

1. Create new configuration file for your website:

   `/etc/nginx/sites-enabled/<domain-name>`

   NOTE: It's a good practice to name the file with a associated domain name. It's not mandatory but helps organizing config files.

2. Using the config file you've just created configure virtual server which runs the website:

   ```nginx
   server {
       root /var/www/html;
       index index.html;

       server_name <domain-name>;

       location / {
           proxy_pass http://localhost:<port>;
       }
   }
   ```

   NOTE: Port is the port your app runs on a localhost (127.0.0.1). It's used to expose the localhost app to the web.

3. Update your DNS nameserver

   Since you are managing your domain name on the VPS you need to inform the DNS provider about it. In the domain configuration page look for Nameserver configuration and provide custom addresses associated with your VPS provider.

   Example for DigitalOcean VPS provider:

   - ns1.digitalocean.com
   - ns2.digitalocean.com
   - ns3.digitalocean.com

4. Attach the website config in the global Nginx config

   You need to let the Nginx know about the config you've just created.

   Sudo-open the `/etc/nginx/nginx.conf` file, look for _Virtual Hosts Config_ section, and add the website config file to the list like so:

   > \##
   >
   > \# Virtual Hosts Config
   >
   > \##
   >
   > include /etc/nginx/conf.d/\*.conf;
   >
   > **include /etc/nginx/sites-enabled/<domain-name>;**

5. Test your website

   To test your website open your browser and visit your VPS IP address. You should see the application running on your localhost with the giver port.

   From this point your website still might not be accessible from your domain name. That's because Nginx by default forwards the HTTP requests to HTTPS but we haven't configured it yet. Check _"Adding SSL certificate"_ section for more instructions.

## 4. VPS - firewall, auto-updates

1. Configuring the firewall.

   In this example we are using UFW (Uncomplicated Firewall). Generally speaking firewall on server allows you to manage incomming types of connections and forbid them if intended.

   First check the current status of ufw and ensure it's present on your server. It should say that the service is inactive:

   ```bash
   sudo ufw status
   ```

   Then indiacate which types of connection you'd like to allow. For now we will enable SSH and HTTP.

   ```bash
   sudo ufw allow ssh && sudo ufw allow http
   ```

   Finally, enable the firewall with given configuration:

   NOTE: Please ensure you allowed the SSH connections. If not, you might lose the access to the server by enabling the firewall.

   ```bash
   sudo ufw enable
   ```

2. Setting up auto updates

   With use of unattended-upgrades package you can setup service that will update you server automatically.

   First, install the package:

   ```bash
   sudo apt install unattended-upgrades
   ```

   Finally setup and start the service:

   ```bash
   sudo dpkg-reconfigure --priority=low unattended-upgrades
   ```

## 5. Adding SSL certificate

    DNS providers usually offers the SSL encryption as an extra paid feature. Suprisingly SSL certificate can be generated for free by the [Certbot](https://certbot.eff.org/) in few easy steps that varies depend on your servers architecture.

    Certbot will fully automatically generate and apply certificates to your website(s).

    Just visit the [instructions page](https://certbot.eff.org/instructions), choose detailed informations about your server and follow given instructions.

    TIP: If you are using UFW remember to allow HTTPS ports (443):

    ```bash
    sudo ufw allow https
    ```

## 6. Configuring NodeJS process manager for your app

    TODO
