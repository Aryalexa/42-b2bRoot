### 6. Bonus

#### I. WordPress and services

> 🌳
> 
> WordPress is a popular open-source content management system (CMS)
> - IT allows users to create and manage websites and blogs.
> - It is built on PHP and uses a MySQL or MariaDB database to store content and configurations.
> - It provides a user-friendly interface, a wide range of themes and plugins, and flexibility.
> - It is a popular choice for both beginners and experienced web developers.
>
> To set up a WordPress site, we need the following componenents:
> - A web server to serve the site. (Apache and Nginx are commonly used web servers. We have to use Lighttpd).
> - WordPress is written in PHP, so we need a PHP runtime installed to execute PHP.
> - A database server to store its content and configuration.
> - WordPress files (from the official website) to store in the server.
> - configure wp: database and `wp-config.php`
> - configure web server (to serve the WordPress files)
> - Decide on the domain or IP address where you want to access your WordPress site.
> - A web browser to access and interact with your WordPress site (we'll use a host browser)

Web server: Lighttpd
> 🌳
> 
> Lighttpd
> - It is an open-source web server designed for speed, efficiency, and flexibility.
> - It uses an event-driven architecture, making it suitable for handling many simultaneous connections with low resource usage
> - It supports URL rewriting, allowing administrators to configure clean and user-friendly URLs.
> - Its design is modular, allowing administrators to enable or disable specific modules based on their requirements.
> - It is well-suited for scenarios where resource efficiency and high performance are critical, such as serving static content, handling a large number of concurrent connections, or acting as a reverse proxy.  

- Install it `sudo apt install`.
- Allow connection on port 80 `sudo ufw allow 80`. (use `sudo ufw status` to check)
- Forward the port
  - go to VM >> `Settings` >> `Network` >> `Adapter 1` >> `Advanced` >> `Port Forwarding`.
  - Type `http://127.0.0.1:8080` or `http://localhost:8080` in your host browser. You should see a Lighttpd "placeholder page".
 
WordPress
- In order to install WP, we need wget and zip.
- Change directory `cd /var/wwww` to install WP here.
- Get the download link for the latest version of WP at [wordpress.org/download](https://wordpress.org/download/), choose zip format.
- Download WP, unzip the package and rename paths.
- give `html` permissions: 755 (rwx for owner user, r-x for group and others)
```
$ sudo apt install wget zip
$ cd /var/wwww
$ sudo wget https://es.wordpress.org/latest-es_ES.zip
$ sudo unzip latest-es_ES.zip
$ sudo mv html/ html_old/
$ sudo mv wordpress/ html
$ sudo chmod -R 755 html
```

> 🌳
> 
> `wget` is a command-line utility to download files from the web
>
> `zip` is a command-line utility used for compressing and archiving files and directories in a ZIP format

MariaDB
- ...

#### II. A useful service of your choice: X

> What are NGINX and Apache2?
> open-source web servers
> - Apache has been a dominant player in the web server space for a long time and is well-suited for a wide range of scenarios.
> - Apache supports a variety of features, including the ability to serve static and dynamic content, handle virtual hosts, and provide support for various modules and extensions
> - Nginx is a high-performance, open-source web server and reverse proxy server.
> - Nginx is designed to be lightweight, scalable, and efficient, making it particularly suitable for serving static content and handling a large number of concurrent connections.
