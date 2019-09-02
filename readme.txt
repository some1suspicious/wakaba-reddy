Welcome to Wakaba-reddy, pre-release version.

There's no proper documentation yet.

The script behaves a lot like wakaba. I stole most of the
translations from other wakaba repositories. Many thanks to parashevody for their work on that.

The script lives in a directory that needs to be writable by the web
server process. This might mean you need to set it to full permission,
777. It wants three subdirectories, src, thumb and res, and those need
to be writeable too.

Edit the config.pl files according to your needs. You should at least
configure the admin password and SQL settings.

For SQL backends, the script has been designed to be able to use either
mysql or sqlite. You'll need to install Perl drivers for the one you
choose. 

You should have ImageMagick "convert" command available for thumbnailing.

It also either needs the Digest::MD5 module, which is standard in
newer Perl versions but not in older, or the external "md5sum"
command. To make charset conversion work properly, you need Perl 5.8.0
or higher. On older perl versions, it tries to work entirely in ISO
Latin-1, but this is untested, like much else. Furthermore, you
should make your webserver send the proper charset code for the
pages the script creates. With Apache, make a .htaccess files with
"AddCharset utf-8 .html" in it.

When the script is run the first time, it tries to create its
databases, and make the first HTML page. Hopefully this works,
otherwise you might need to mess around with it in uncomfortable ways.
The admin interface as an option to re-make all the static HTML pages,
which is necessary if the script is moved, or if you change options in
the config file that affect the pages directly. If everything breaks
and you can't get to the admin interface, try accessing
http://.../wakaba.pl?action=rebuild&admin=ADMINPASSWORD

Well, good luck. Report problems and bugs to nobody. Nobody will help you.

- some1suspicious

















Now seriously, the purpose of this project is to make wakaba great aga... Wait, it's already great. Well, it's not perfect. Especially if you don't want to mess with it too much to just get it up and running.

* It lacks a lot of simple features that you might have seen earlier on imageboards using almost original wakaba. I'm not talking about fastCGI support and other stupid and useless (as for year 2019 small imageboard) things. But rather really simple features like noko and a bit more up to date HTML.
* Mod pannel is the most terrible thing you can imagine.
* For some reason the latest version (from !WAHa.06x36's server) contains a few typos that might cause confusion and prevent a newbie from even running it normally right after installing.
* It has several vulnerabilities. It's not about security and privacy. Just admin's minor pain in the ass.

This reasons are enough to modify it a bit and leave publically accessible.
* Not to completely rewrite it and make it a totaly different thing.
* Not to add fancy sh1t and think that someone might be interested in that bloated script with tons of dependencies while it's already outdated and complicated inside.


And here is the (almost) complete installation guide.

**INSTALLING OS**

It is recommended to use some staging environment to tweak your engine and see if it works and then take it to production.

I'm using Debian 10 GNU/Linux distro on VirtualBox. It works perfectly fine.

If you're not sure which file you need to download to get the OS from the official website you can try this one: https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-10.0.0-amd64-netinst.iso

After you have installed it you'll need to also enable network access to you virtual instance from the host machine. You can find lots of guides on the Internet. Example: https://2buntu.com/articles/1513/accessing-your-virtualbox-guest-from-your-host-os

Then make sure that you have sudo access (and if you have sudo package at all).

Get sudo:
**$** `su`
password:
**#** `apt update && apt install sudo`

Then add your user to sudo group
**#** `cd /usr/sbin`
**#** `./usermod -a -G sudo <username>`

Then logout and login again (only then changes would take effect)
**#** `exit`
**$** `exit`
`debian login:`
`Password:`
**$**

Check if your user is in sudoers group (output should include your username)
**$** `getent group sudo`

Now you can install Apache server (it is already installed if you have installed your OS with the right options)
**$** `sudo apt install apache2`

You can check it's status by
**$** `sudo systemctl status apache2`
or by just requesting the default page using some client like curl and see if it throws raw HTML at you in response
**$** `sudo apt install curl && curl localhost:80`

Then check if you can access it form your host system (find guests IP by running "**$** `ip address`" command inside of it). And make sure that your network config file is configured properly. As virtual box won't change it if you have added guest's netwrok settings after you have installed the OS. Check the config located at `/etc/network/interfaces` edit it if needed and raise interfaces again (or just reboot).

You probably can not access it at this point as by default the firewall will not allow any connections at all.
You need to allow TCP protocol port 80
**$** `sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`

Now you should be able to type your guest's IP address in your host's browser's address bar and hopefully navigate to the default Apache page. If you can't then you missed something or did something wrong. Try to fix it.

**INSTALLING THE ENGINE**

Install git
**$** `sudo apt install git`
Clone the repo (somewhere inside of the `/home/<username>/` directory so you don't need the root access rights to edit that files because you might want to tweak it, name your boards and only install it afterwards) 
**$** `git clone https://github.com/some1suspicious/wakaba-reddy.git`
Move this folder to `/var/www/html/ `
**$** `sudo mv wakaba-reddy /var/www/html/<your board name>`

This way you can quickly create as many boards as you need. And if you're following this guide while doing everything on a real server with Internet access then **CONGRATULATIONS** now everyone is able to view every file you have there including your configs with passwords. So don't do this step before the next one. Never. You need to at least turn off your apache server first (**$** `sudo service apache2 stop` ). But it's fine if you're on virtual machine.

**Configure your Apache server**
1. **Timeout**
Default timeout is 300 seconds. This might to be too long and makes your server a nice target for a "low and slow" attacks (https://www.cloudflare.com/learning/ddos/ddos-low-and-slow-attack/).
Make it 60 instead of 300. This means that your users won't be able to upload or receive something for more than 60 seconds.

2. **CGI execution, .htaccess**
To allow the CGI execution (CGI is an interface for Apache-Perl communication) and make your rules in .htaccess files work you need to find `<Directory /var/www/>` tag and edit it so it looks like this:
`# ------------------------------------------------`
`<Directory /var/www/>`
        Options Indexes FollowSymLinks
        AllowOverride All
        Options +ExecCGI
        AddHandler cgi-script .cgi .pl
        Require all granted
`</Directory>`
`# ------------------------------------------------`

3. **Additional security tweaks**
You might want to place this right before the last line of the config that says something about vim.

`# ------- Server security config  ---------------`
`# Prevent coockies stealing`
`TraceEnable off`
`# Disable showing Apache version in headers`
`ServerSignature Off`
`ServerTokens Major`
`# Prevent from obtaining inode number, multipart MIME boundary, and child process through Etag header`
`FileETag None`
`# Set cookie with HttpOnly and Secure flag (prevent possible CSS, stealing sessions and coockies)`
`Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure`
`#CORS (forbid framing)`
`Header always append X-Frame-Options SAMEORIGIN`
`# X-XSS protection`
`Header set X-XSS-Protection "1; mode=block"`
`# Disable HTTP 1.0 (allow only 1.1)`
`RewriteEngine On`
`RewriteCond %{THE_REQUEST} !HTTP/1.1$`
`RewriteRule .* - [F]`
`# ------------------------------------------------`
But this is not the best place to put it. We are doing this for simplicity but logically it is better to put this parts in `/etc/apache2/conf-available/security.conf` and then enable them. Restart apache2 service after editing it's configuration files (**$** `sudo service apache2 restart` ).

4. **Apache modules**
There are some modules that can extend the apache's core functions with additional features.
You can enable them by the following command
**$** `sudo a2enmod <modname_without_extension>`
And aside from the default ones, you will probably need the following modules:
**cgid** (https://httpd.apache.org/docs/2.4/mod/mod_cgid.html)
**headers** (https://httpd.apache.org/docs/2.4/mod/mod_headers.html)
**rewrite** (https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)
**ssl** (https://httpd.apache.org/docs/2.4/mod/mod_ssl.html)
Restart the apache2 service after enabling them.

5. **Sites configuration**
There is a folder (`/etc/apache2/sites-available` ) that contain configuration for your websites. You can host multiple sites but for simplicity we will leave it unchanged. But you can specify `ServerName` and `ServerAdmin` (email) if you want. Note: if you want to host multiple sites then you might need to change that main config files because we have configured it only for one website and you might need more diverse options for each website. 

6. **Actual website's directory**
You can place `robots.txt` files here (`/var/www/html/`) and some `.htaccess` files for security reasons (remember that it is always better to place rules as high in hierarchy (main apache.conf > virtual host > .htaccess) as possible while it's reasonable because it's more secure than using .htaccess files for everything).

7. **htaccess**
If you are using .htaccess from wakaba-reddy repo then it is probably configured already. But keep in mind that you might need to edit it after you rename/move/add some files or directories while doing some changes to the engine.

8. **chmod**
Script files should be executable. You can make them so with this command:
**$** `cmod 755 <filename>`
Some direcotories (including the website's root directory) should be writable so Perl scripts can write files to them. Make directories writable with this command:
**$** `chmod 777 <filename>`

**Provide database**
Wakaba supports MySQL and SQLite databases. In this tutorial we'll use MariaDB (it is MySQL's fork).
**$** `sudo apt install mariadb-server`
And check status, it should be running
**$** `sudo systemctl status mariadb`

Now you should login as root and create a new user and then grant him all priviliges.
**$** `sudo mariadb`
**>** `CREATE DATABASE <my_database_name> CHARACTER SET utf8 COLLATE utf8_general_ci;`
**>** `CREATE USER '<my_user_name>'@'localhost' IDENTIFIED BY '<my_password>';`
**>** `GRANT ALL PRIVILEGES ON <my_database_name>.* TO '<my_user_name>'@'localhost';`
**>** `exit`

Check if you actually can login and create a database:
**$** `mariadb --user=<my_user_name> -p`
`password:`
**>** `use <my_database_name>;`
**>** `CREATE TABLE test_table (tst INT);`
**>** `SELECT * FROM test_table;`
If that doesn't produce any errors then you're good to go.

Go ahead and hit `http://<your_domain_or_local_ip>/<board_name>/wakaba.pl`
It should create the tables in the database and the first page with the interface. If something gone wrong then it's probably your fault. Shame on you. Or maybe you've just forgotten to edit the `config.pl` file. Tweak it according to your preferences. And also install imagemagick if you will use it (see the actual `congif.pl` ).
