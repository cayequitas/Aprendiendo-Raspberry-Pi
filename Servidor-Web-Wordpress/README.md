# Configuración de un servidor web en el Raspberry pi 

Configure su Raspberry pi como un servidor web y utilícelo para instalar wordpress o cualquier CMS

## Paso 1: Instalar el servidor web Apache

Apache es un servidor web de código abierto, que implementa el protocolo HTTP; es bastante popular y utilizado para alojar archivos HTML con módulos adicionales que pueden servir para la realización de páginas web dinámicas usando lenguajes como PHP. Y lo mejor de todo es que lo puedes instalar en tu Raspberry Pi.


### Instalar Apache

Primero debe instalar el paquete `apache2`por medio del comando apt-get en la terminal, teniendo en cuenta que se debe hacer desde el super usuario o root `sudo`, de esta manera:

```
sudo apt-get install apache2 -y
```

### Probar el servidor web

De manera predeterminada, Apache pone un archivo HTML de prueba en el folder web. Esta página web predeterminada se puede encontrar en el servidor cuando entras a un browser y buscas `http://localhost/` dentro del Raspberry Pi, y también se puede hacer remotamente desde otra computadora con la dirección IP del Raspberry Pi, en este caso sería `http://192.168.2.5`(acá la remplazaría con la dirección IP que le aparezca su Raspberry Pi). Para averiguar la dirección IP de su Pi, en la terminal escriba el comando `hostname -I` o si no tiene mucho conocimiento sobre la terminal y es usuario OSX le recomiendo mirar la aplicación LanScan(la cual pueden descargar desde el App store GRATIS!) y con sólo darle play puedes ver la dirección IP de tu Pi, cómo en la imágen de abajo.

![](images/LanScan.png)

Entre a la página web predeterminada, desde el Pi o desde otro computador que este en la red y usted debería ver lo siguiente:

![](images/apache-it-works.png)

Esto significa que esta corriendo bien Apache!

#### Cambiando la página web predeterminada

Esta página web predetermina es un archivo HTML dentro de su filesystem; y esta ubicado en `/var/www/index.html`. Entre a este directorio desde la terminal por medio del comando `cd` y después mire que hay adentro por medio del comando `ls` de esta manera:


```
cd /var/www
ls -al
```

Esto le mostrará:

```
total 12
drwxr-xr-x  2 root root 4096 Jan  8 01:29 .
drwxr-xr-x 12 root root 4096 Jan  8 01:28 ..
-rw-r--r--  1 root root  177 Jan  8 01:29 index.html
```
Esto quiere decir que existe un archivo en `/var/www/` que se llama `index.html`. El `.` se refiere al directorio en el que se encuentra `/var/www/` y los `..` se refiere al parent directory `/var/`.

#### El significado de las Columnas

1. Los permisos del archivo o los directorios
2. El número de archivos en un directorio (o `1` si es un solo archivo)
3. El usuario propietario del archivo o del directorio
4. El grupo que es dueño del archivo o el directorio
5. El tamaño del archivo
6. La última modificación, fecha y hora

Como se puede ver, el directorio `www` y el archvico `index.html` son archivos del superusuario o `root` user. Para editar el archivo, se debe tener el permiso antes de empezar a editar, para esto se puede hacer de dos maneras. La primera se cambia de usuario antes de editar el archivo usando `sudo chwon pi: index.html` o también se puede con el comando `sudo` directamente `sudo nano index.html`.

Trate de editar este archivo y después darle refresh a su browser para ver los cambios en la página web.

## Paso 2: Instalar PHP

PHP es un pre-procesador; es un codigo que corre cuando el servicodr recibe un request de una página web. Este controla que es lo que necesita mostrar en la página, luego se la manda directamente al browser.
------------------1
PHP is a preprocessor; it's code that runs when the server receives a request for a web page. It runs, works out what needs to be shown on the page, then sends that page to the browser. Unlike static HTML, PHP can show different content under different circumstances. Other languages are capable of this, but since WordPress is written in PHP, that's what we need to use this time. PHP is a very popular language on the web; large projects like Facebook and Wikipedia are written in PHP.

Instalar PHP y los paquetes de Apache con los siguientes comandos:

```
sudo apt-get install php5 libapache2-mod-php5 -y
```

### Probar PHP

Ahora debe mover el archivo `index.html` a `index.php` de la siguiente manera:


```
sudo mv index.html index.php
```

Edite este archivo desde el super usuario `sudo`: 

```
sudo nano index.php
```

Ahora agregue un poco de contenido PHP en el archivo, así:


```
<?php echo "hello world";
```
Grabe y después hagale refresh a su browser. debería ver "hello world". Este no es un ejemplo de una página web dinámica pero es hecha en PHP. Intentemos algo dinámico, por ejemplo:

```
<?php echo date('Y-m-d H:i:s');
```
O digámole que nos muestre nuestra información de PHP:

```
<?php phpinfo();
```

## Paso 3: Instalar MySQL

MySQL (pronounced *My Sequel* or *My S-Q-L*) is a popular database engine. Like PHP, its overwhelming presence on web servers enhanced its popularity. This is why projects like WordPress use it, and why those projects are so popular.

Install the MySQL Server and PHP-MySQL packages by entering the following command into the terminal:

```
sudo apt-get install mysql-server php5-mysql -y
```

When installing MySQL you will be asked for a root password. You'll need to remember this to allow your website to access the database.

## Paso 4: Descargar WordPress
----------------2

You can download WordPress from [wordpress.org](http://wordpress.org/) using the `wget` command. Helpfully, a copy of the latest version of WordPress is always available at [wordpress.org/latest.tar.gz](https://wordpress.org/latest.tar.gz) and [wordpress.org/latest.zip](https://wordpress.org/latest.zip), so you can grab the latest version without having to look it up on the website. At the time of writing, this is version 3.8.1.

Navigate to `/var/www/`, and download WordPress to this location. You'll need to empty the folder first (be sure to check you're not deleting files you need before running `rm`); change the ownership of this folder to the `pi` user too.

```
cd /var/www
chown pi: .
rm *
wget http://wordpress.org/latest.tar.gz
```

Now extract the tarball, move the contents of the folder it extracted (`wordpress`) to the current directory and remove the (now empty) folder and the tarball to tidy up:

```
tar xzf latest.tar.gz
mv wordpress/* .
rm -rf wordpress latest.tar.gz
```

Running the `ls` or (`tree -L 1`) command here will show you the contents of a WordPress project:

```
.
├── index.php
├── license.txt
├── readme.html
├── wp-activate.php
├── wp-admin
├── wp-blog-header.php
├── wp-comments-post.php
├── wp-config-sample.php
├── wp-content
├── wp-cron.php
├── wp-includes
├── wp-links-opml.php
├── wp-load.php
├── wp-login.php
├── wp-mail.php
├── wp-settings.php
├── wp-signup.php
├── wp-trackback.php
└── xmlrpc.php
```

This is the source of a default WordPress installation. The files you edit to customise your installation belong in the `wp-content` folder.

## Step 5: Set up your WordPress Database

To get your WordPress site set up, you need a database. Run the `mysql` command in the terminal and provide your login credentials (e.g. username `root`, password `password`):

```
mysql -uroot -ppassword
```

Here I have provided my password (the word `password`) on the command line; there is no space between `-p` and your password.

Alternatively you can simply supply an empty `-p` flag and wait to be asked for a password:

```
mysql -uroot -p
```

Now you will be prompted to enter the root user password you created earlier.

Once you're connected to MySQL, you can create the database your WordPress installation will use:

```
mysql> create database wordpress;
```

Note the semi-colon ending the statement. On success you should see the following message:

```
Query OK, 1 row affected (0.00 sec)
```

Exit out of the MySQL prompt with `Ctrl + D`.

## Step 6: WordPress Configuration

You need to find out your Pi's IP address to access it in the browser, so in a terminal type the command `hostname -I`.

Navigate to `http://YOUR-IP-ADDRESS` e.g. `http://192.168.1.5` in the web browser on your Pi.

You should see a WordPress error page; this is good! Click the big button marked `Create a Configuration File` followed by the `Let's go!` button on the next page.

Now fill out the basic site information as follows:

```
Database Name:      wordpress
User Name:          root
Password:           <YOUR PASSWORD>
Database Host:      localhost
Table Prefix:       wp_
```

Upon successful database connection, you will be given the contents of your `wp-config.php` file:

![](images/wp-config.png)

Copy this text, return to the terminal on the Pi and edit the file with `nano wp-config.php`. Paste the text into this file, and save and exit with `Ctrl + X`, then `Y` for yes and `Enter`.

Now hit the `Run the install` button.

### Welcome screen

Now you're getting close.

![](images/wp-info.png)

Fill out the information: give your site a title, create a username and password, put in your email address and untick the search engines box. Hit the `Install WordPress` button, then log in using the account you just created.

Now you're logged in and have your site set up, you can see the website by visiting your IP address in the browser on the Pi or another computer on the network. To log in again (or on another computer), go to `http://YOUR-IP-ADDRESS/wp-admin`.

### Friendly permalinks

It's recommended that you change your permalink settings to make your URLs more friendly. To do this, log in to WordPress and go to the dashboard. Go to `Settings` then `Permalinks`. Select the `Post name` option and click `Save Changes`. After saving, you will be prompted to update your `.htaccess` file. You probably don't have one yet, so add one in `/var/www/` by typing `nano .htaccess`; note this is a hidden file, so it starts with a dot. Then paste in the contents provided:

```
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
```

Save the file and return to the website homepage. Click on the post title or the sample page link and you'll probably see a `Not Found` error page. This is because the `rewrite` module has not been enabled in Apache. To do this, enter `sudo a2enmod rewrite`.

You'll also need to tell the virtual host serving the site to allow requests to be overwritten. Do this by editing the virtual host file (with root permissions): `sudo nano /etc/apache2/sites-available/default`; also, change the `AllowOverride` setting on line 11 (inside the `<Directory /var/www/>` block) from `None` to `All`. Save the file and then restart Apache with `sudo service apache2 restart`. Once it's restarted, refresh the page and it should load successfully. Now posts have URLs like `/hello-world/` instead of `/?p=123`, and pages have URLs like `/sample-page/` instead of `/?page_id=2`.

### Customisation

WordPress is very customisable. By clicking your site name in the WordPress banner along the top of the page (when logged in), you'll be taken to the Dashboard. From here you can change the theme, add pages and posts, edit the menu, add plugins and lots more. This is just a taster for getting something interesting set up on the Raspberry Pi's web server.

## Licence

Unless otherwise specified, everything in this repository is covered by the following licence:

![Creative Commons License](http://i.creativecommons.org/l/by-sa/4.0/88x31.png)

***Web Server Setup and WordPress*** by the [Raspberry Pi Foundation](http://raspberrypi.org) is licenced under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

Based on a work at https://github.com/raspberrypilearning/web-server-wordpress
