### 6. Bonus
Parts
- [Wordpress web service](https://github.com/Aryalexa/42-b2bRoot/edit/main/6-Bonus.md#i-wordpress-and-services)
- [A useful service of your choice](https://github.com/Aryalexa/42-b2bRoot/edit/main/6-Bonus.md#ii-a-useful-service-of-your-choice)


## I. WordPress and services
We want a functional WordPress site with Lighttpd, MariaDB and PHP.

> 🌳
> 
> WordPress is a popular open-source content management system (CMS)
> - It allows users to create and manage websites and blogs.
> - It is built on PHP and uses a MySQL or MariaDB database to store content and configurations.
> - It provides a user-friendly interface and a wide range of themes and plugins.
> - It is a popular choice for both beginners and experienced web developers.
>
> To set up a WordPress site, we need the following componenents:
> - A **web server** to serve the site. (Apache and Nginx are commonly used web servers. We have to use Lighttpd).
> - **WordPress** files (from the official website) to store in the server.
> - A **database server** to store its content and configuration.
> - WordPress is written in **PHP**, so we need a PHP runtime installed to execute PHP.
> - A **web browser** to access and interact with your WordPress site (we'll use a host browser)

##### 🔸 Web server: Lighttpd
- Install it `sudo apt install`.
- Allow connection on port 80 `sudo ufw allow 80`. (use `sudo ufw status` to check)
- Forward the port
  - go to VM >> `Settings` >> `Network` >> `Adapter 1` >> `Advanced` >> `Port Forwarding` and add the port.
  - Type `http://127.0.0.1:8080` or `http://localhost:8080` in your host browser. You should see a Lighttpd "placeholder page".

> 🌳
> 
> Lighttpd
> - It is an open-source web server designed for speed, efficiency, and flexibility.
> - It uses an event-driven architecture, making it suitable for handling many simultaneous connections with low resource usage
> - It supports URL rewriting, allowing administrators to configure clean and user-friendly URLs.
> - Its design is modular, allowing administrators to enable or disable specific modules based on their requirements.
> - It is well-suited for scenarios where resource efficiency and high performance are critical, such as serving static content, handling a large number of concurrent connections, or acting as a reverse proxy.  

##### 🔸 WordPress
- In order to install WP, we need to install wget and zip first.
- Change directory `cd /var/www` to install WP here.
- Get the download link for the latest version of WP at [wordpress.org/download](https://wordpress.org/download/), choose zip format.
- Download WP with wget, unzip the package and rename paths.
- give `html` permissions: 755 (rwx for owner user, r-x for group and others)
```
$ sudo apt install wget zip
$ cd /var/www
$ sudo wget https://es.wordpress.org/latest-es_ES.zip
$ sudo unzip latest-es_ES.zip
$ sudo mv html/ html_old/
$ sudo mv wordpress/ html/
$ sudo chmod -R 755 html/
```

> 🌳
> 
> `wget` is a command-line utility to download files from the web
>
> `zip` is a command-line utility used for compressing and archiving files and directories in a ZIP format

##### 🔸 Database: MariaDB
- Install it with apt.
- Then do the MySQL secure installation.

```shell
$ sudo apt install mariadb-server
$ sudo mysql_secure_installation
```

> 🌳
> 
> MariaDB
> 
> It is an open-source relational database management system (RDBMS).
> Its goal is to remain highly compatible with MySQL while providing additional features, performance improvements, and enhanced security.
>
> `mysql_secure_installation`
> - It is a script provided with MySQL and MariaDB that helps to secure the installation by performing various security-related tasks. The configuration includes:
>   - It asks whether we want to use `unix_socket` authentication. Instead of relying on a username and password combination, this method leverages the operating system's user authentication mechanism, specifically Unix sockets. >> No
>     - It would check if the provided username corresponds to a valid system user on the operating system where the server is running.
>     - Unix_socket authentication is specific to Unix-like operating systems (such as Linux).
>     - Additionally, it only applies when the client and server are both on the same host, as it relies on local Unix sockets for communication.
>     - a socket is a combination of an IP address and a port number that uniquely identifies a communication channel between two processes.
>   - If there is no root password set, the script will prompt you to set a password for the root user >> No (no password) or Yes (and provide the password). Saying No is OK.
>   - It lets you remove anonymous users, which would connect without providing any credentials. >> Yes
>   - It lets you disallow remote login for the root user, adding a layer of security >> Yes
>   - It lets you remove test databases >> Yes
>   - At last it asks to reload privilege tables to make the changes take effect immediately >> Yes

```
Switch to unix_socket authentication? → N
Change the root password? → N
Remove anonymous users? → Y
Disallow root login remotely? → Y
Remove test database and access to it? → Y
Reaload privilege tables now? → Y
```
- Once MariaDB is installed, create both a DB and a user for WordPress to use.
  - Let's enter "mariadb mode".
  - The DB can be named as you wish.
  - Create the user (the below instruction creates a user with the username 'login' who can connect only from localhost and has the password '12345')
  - Grant the user privileges (all) for the new DB.
  - Use "flush" to refresh the privileges and get last changes.
```
$ mariadb
mdb > CREATE DATABASE my_wp_database;
mdb > SHOW DATABASES;
mdb > CREATE USER 'login'@'localhost' IDENTIFIED BY '12345';
mdb > GRANT ALL PRIVILEGES ON wp_database.* TO 'gemartin'@'localhost';
mdb > FLUSH PRIVILEGES;
mdb > exit
$
```
##### 🔸 PHP
> 🌳
>
> PHP is a server-side scripting language widely used for web development, and a PHP runtime provides the necessary infrastructure for interpreting and executing PHP code.
>
> When you set up a web server to run PHP, you need to install the PHP runtime on the server.
> We need two packages: `php-cgi` and `php-mysql`.
> - `php-cgi`: PHP Common Gateway Interface.
>   - It is a CGI executable, a way to run PHP scripts via CGI.
>   - It is a binary executable that implements the CGI protocol for PHP scripts.
>   - The web server uses it to execute the script and generate dynamic content when a request is received.
>   - It is one way to integrate PHP with web servers.
> - `pdp-mysql`
>   - It is a PHP extension, adding MySQL-specific functionality to PHP.
>   - It provides functions and features for PHP to connect to a MySQL database, execute queries, and manage data.

- Install what is necessary to execute PHP web applications and to allow them to communicate with databases.
  - `sudo apt install php-cgi php-mysql`.

##### 🔸 WordPress configuration
WordPress settings are configured with the file `wp-config.php`.
- Go to `/var/www/html`.
- Use the sample provided to create the config file.
- Start to edit it.

```shell
$ cd /var/www/html
$ cp wp-config-sample.php wp-config.php
$ sudo nano wp-config.php
```
- Edit the following with the MariaDB database and user we created.
  - DB_NAME: `'my_wp_database'`
  - BD_user: `'login'`
  - DB_password: `'12345'`
- Enable the FastCGI module in the Lighttpd web server to improve the performance of PHP scripts. FastCGI is a protocol for interfacing web servers with external programs.
  - `sudo lighty-enable-mod fastcgi`
- Enable FastCGI support specifically for PHP scripts.
  - `sudo lighty-enable-mod fastcgi-php`
- Save changes with `sudo service lighttpd force-reload`.
- To finish the WordPress installation, go to the host browser and type `localhost` (it will enter [localhost/wp-admin/install.php](localhost/wp-admin/install.php) and fill the form.
  - Site title
  - user name (a new user for wordpress?)
  - password
  - email (42 email)
- Select `Installl WordPress` and we are done!


- Access `localhost` again and we'll see a working WP site. OR `http://127.0.0.1:8080`
- To make changes access the admin panel at `localhost/wp-admin` and login.


## II. A useful service of your choice

> We can't choose NGINX or Apache2. What are NGINX and Apache2?
> 
> open-source web servers.
> - Apache has been a dominant player in the web server space for a long time and is well-suited for a wide range of scenarios.
> - Apache supports a variety of features, including the ability to serve static and dynamic content, handle virtual hosts, and provide support for various modules and extensions
> - Nginx is a high-performance, open-source web server and reverse proxy server.
> - Nginx is designed to be lightweight, scalable, and efficient, making it particularly suitable for serving static content and handling a large number of concurrent connections.
>

We can choose whatever we want...
- A different web server like LiteSpeed
- Fail2ban, a security measure for SSH against brute force attacks.
- A database Server (MySQL or PostgreSQL)
- A File Server (vsftpd or ProFTPD)
- A Version Control Server (Git)
- A chat server (Matrix Synapse)
- A monitoring service (Prometheus + Grafana)
- A VPN Server (OpenVPN or WireGuard)
- A Container Orchestration (Docker + Kubernetes)
- A Personal Cloud (Nextcloud or ownCloud)
- A Game Server (Minecraft, Factorio, etc.)

I'm going to explain how to do a file server (with vsftpd - easy), a VPN server (with OpenVPN - not as easy) and a version control server (with git - easy).

#### 🔹 vsftpd (File Server)
VSFTPD (Very Secure FTP Daemon) is a file server.
- A file server is dedicated to storing, managing and sharing files and data across a network. t provides centralized storage and access to files and directories for client computers and users within an organization or network.

1. Install vsftpd
  ```bash
  $ sudo apt update
  $ sudo apt install vsftpd
  ```
2. Configure vsftpd
  - Open the configuration file for editing
  ```bash
  $ sudo nano /etc/vsftpd.conf
  ```
  - Ensure the following configurations are set or modify them accordingly:
  ```conf
  anonymous_enable=NO
  local_enable=YES
  write_enable=YES
  chroot_local_user=YES
  ```
  - These settings disable anonymous access, allow local users to login, enable write permissions, and chroot users to their home directory (restricting the users to their home directories, their new "root").

3. Restart vsftpd
```bash
sudo service vsftpd restart
```

4. Create User Accounts
  - Create a new user for FTP access
  ```bash
  sudo adduser ftpuser
  ```
  - Set a password for the new user.

5. Test FTP Access:
- From your local machine (the host), use an FTP client (e.g., FileZilla) to connect to your server using the created user credentials. Connect to the server's IP address or hostname.

#### 🔹 Git (version control server)
1. Install git
```
sudo apt update
sudo apt install git
```
2. Create a Git user "git".
- It's a good practice to create a dedicated user for Git operations.
- Use this user in the VM from now on (for this configuration).
```bash
sudo adduser git
```
3. Set up SSH access
- Git typically uses SSH for authentication and secure communication. You'll need to generate an SSH key pair and configure SSH access for your Git user.

  a. Generate an SSH key pair on our machine (not the VM)
  - You should generate the SSH key pair on the machine from which you'll be accessing the Git server.
  - You will generate a SSH key pair (id_rsa and id_rsa.pub) in the ~/.ssh/ directory.
  ```bash
  ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
  ```
  
  b. Copy the Public Key to the Git Server.
  - We want to copy the public key (id_rsa.pub) to the Git user's .ssh directory on the server in the VM (authorized_keys file).
    - This allows the Git server to authenticate your SSH connections using your private key. You can use `scp` (secure copy protocol) or `ssh-copy-id` (it copies public keys to their appropiate place) commands.
  - Create the .ssh directory in your VM.
  ```bash
  # vm
  sudo mkdir /home/git/.ssh
  ```
  - Copy the public key. Option 1: use `scp` to copy and then concat the key at the end of the authorized_keys file (Use > instead of >> if it's the first time, so the file is created).
  ```bash
  # Mac
  scp ~/.ssh/id_rsa.pub git@localhost:/home/git/.ssh/ -P 4242
  # vm
  sudo cat /home/git/id_rsa.pub >> /home/git/.ssh/authorized_keys
  sudo chown -R git:git /home/git/.ssh
  ```
  - Copy the public key. Option 2: `ssh-copy-id` sends the the public key directly to the file authorized_keys. It also changes the permissions of the user's home directory and its content in the server.
  ```
  # Mac
  ssh-copy-id -i ~/.ssh/id_rsa.pub git@localhost -p 4242
  ```
  
4. Set up a Git repository
  - First, create a directory (`srv/git`) for your Git repositories and initialize a new bare Git repository
    - The /srv directory is commonly used for site-specific data that is served by the system (/srv/git, /var/git, or /home/git are ok).
  - Then, initialize a new bare Git repository. This will serve as the central repository for your project.
    - A bare Git repository is a repository that does not have a working directory. It only contains the Git version control metadata (commits, branches, tags, config files).
      - They are used as central repositories in a collaborative development environment.
      - Since they do not have a working directory, they can be safely shared and accessed by multiple users concurrently.
      - They are optimized for use on servers where direct file manipulation is not required. They are commonly used for hosting remote repositories accessed via SSH or other protocols.
  ```
  # vm
  sudo mkdir /srv
  sudo mkdir /srv/git
  sudo chown git:git /srv/git
  cd /srv/git
  sudo git init --bare myproject.git
  ```
5. Test the Git server
- Clone the repository from your local machine.
- Git supports ssh (`ssh://[user@]host.xz[:port]/path/to/repo.git/`), git, http, and https protocols.
```
# Mac
git clone ssh://git@localhost:4242/srv/git/myproject.git
```

#### 🔹 OpenVPN (VPN Server) ** ⚠️ Not tested!**
A VPN (Virtual Private Network) is a technology that enables secure and private communication over a public network, typically the internet.
- It creates a private network connection between a user's device (such as a computer, smartphone, or tablet) and a VPN server, encrypting all data transmitted between the two endpoints.
  - Example: VPNs create a secure tunnel between the user's device and a corporate network, allowing remote employees or travelers to access network resources securely over the internet.
- A VPN can help protect the user's online privacy by masking their IP address and concealing their online activities from their internet service provider (ISP) and other third parties.
  - By connecting to a VPN server located in a different geographic region,
    - users can effectively anonymize their internet traffic and browse the web anonymously.
    - users can bypass geographical restrictions and access geo-blocked content as if they were physically located in the server's location.
- They enhance security on public Wi-Fi networks.
  - When connected to public Wi-Fi networks, users are often vulnerable to security threats, such as eavesdropping, data interception, or man-in-the-middle attacks.
  - By using a VPN, users can encrypt their internet traffic and protect themselves from these threats when accessing the internet on public Wi-Fi networks.
- More info [to get a VPN running quickly with minimal configuration](https://openvpn.net/community-resources/static-key-mini-howto/)
- During the setup and testing process, it's essential to monitor the OpenVPN logs for any errors or warnings. You can find the logs in the /var/log/openvpn/ directory.

1. Install OpenVPN:
  ```bash
  sudo apt update
  sudo apt install openvpn
  ```
2. Configure OpenVPN
  - This involves creating the server configuration file, generating cryptographic keys and certificates, and configuring firewall rules if necessary.
  - OpenVPN provides detailed documentation and example configuration files to help you set up the server. You can find the documentation on the OpenVPN website (or [github](https://github.com/OpenVPN/openvpn/tree/master/sample/sample-config-files)) or in the /usr/share/doc/openvpn/examples directory.
  - Copy the sample configuration files:
  ```bash
  sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz /etc/openvpn/
  sudo gzip -d /etc/openvpn/server.conf.gz
  ```
  - Edit the configuration file:
  ```bash
  sudo nano /etc/openvpn/server.conf
  ```
  - Ensure the following lines are uncommented or add them.
    - These lines redirect all client traffic through the VPN, and set DNS options: use specific DNS servers (208.67.222.222 and 220 as backup) to do the addresses translations).
  ```conf
  push "redirect-gateway def1 bypass-dhcp"
  push "dhcp-option DNS 208.67.222.222"
  push "dhcp-option DNS 208.67.220.220"
  ```
  - Generate a static key for additional security:
  ```bash
  openvpn --genkey --secret /etc/openvpn/static.key
  ```
3. Start OpenVPN:
  ```bash
  sudo service openvpn start
  # or
  sudo systemctl start openvpn@server.service
  ```
  - Ensure OpenVPN starts at boot (by enabling it as a systemd service)
  ```bash
  sudo systemctl enable openvpn
  ```
4. Generate Client Configuration:
  - You'll need an OpenVPN client software installed on the client devices.
    - You can also download OpenVPN clients from the OpenVPN website or use third-party OpenVPN clients available for various operating systems.
  - On the server, generate a client configuration.
  ```bash
  sudo nano /etc/openvpn/easy-rsa/keys/client.ovpn
  ```
  - Add the following content, replacing your_server_ip with your server's IP address:
  ```conf
  client
  dev tun
  proto udp
  remote your_server_ip 1194
  resolv-retry infinite
  nobind
  persist-key
  persist-tun
  ca ca.crt
  cert client.crt
  key client.key
  remote-cert-tls server
  ```
  - Save the file.

5. Securely Transfer Client Configuration:
  - Transfer the client.ovpn, client.crt, client.key, and static.key files to the client machine securely.

6. Connect from Client:
  - On the client, use an OpenVPN client to connect using the provided configuration. If the connection is successful, you should be able to access resources on the VPN server's network and browse the internet through the VPN connection.

