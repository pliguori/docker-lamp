docker-lamp
=================

Out-of-the-box LAMP image (PHP+MySQL)


Usage
-----

To create the image `pliguori/lamp`, execute the following command on the docker-lamp folder:

	docker build -t pliguori/lamp .

You can now push your new image to the registry:

	docker push pliguori/lamp


Running your LAMP docker image
------------------------------

Start your image binding the external ports 80 and 3306 in all interfaces to your container:

	docker run -d -p 80:80 -p 3306:3306 pliguori/lamp

Test your deployment:

	curl http://localhost/

Hello world!


Loading your custom PHP application
-----------------------------------

In order to replace the "Hello World" application that comes bundled with this docker image,
create a new `Dockerfile` in an empty folder with the following contents:

	FROM pliguori/lamp:latest
	RUN rm -fr /app && git clone https://github.com/username/customapp.git /app
	EXPOSE 80 3306
	CMD ["/run.sh"]

After that, build the new `Dockerfile`:

	docker build -t username/my-lamp-app .

And test it:

	docker run -d -p 80:80 -p 3306:3306 username/my-lamp-app

Test your deployment:

	curl http://localhost/

That's it!


Connecting to the bundled MySQL server from within the container
----------------------------------------------------------------

The bundled MySQL server has a `root` user with no password for local connections.
Simply connect from your PHP code with this user:

	<?php
	$mysql = new mysqli("localhost", "root");
	echo "MySQL Server info: ".$mysql->host_info;
	?>


Connecting to the bundled MySQL server from outside the container
-----------------------------------------------------------------

The first time that you run your container, a new user `admin` with all privileges
will be created in MySQL with password "pass"

You can then connect to MySQL:

	 mysql -uadmin -ppass

Remember that the `root` user does not allow connections from outside the container -
you should use this `admin` user instead!


Setting a specific password for the MySQL server admin account
--------------------------------------------------------------

If you want to use a preset password instead of a random generated one, you can
set the environment variable `MYSQL_PASS` to your specific password when running the container:

	docker run -d -p 80:80 -p 3306:3306 -e MYSQL_PASS="mypass" pliguori/lamp

You can now test your new admin password:

	mysql -uadmin -p"mypass"


Disabling .htaccess
--------------------

`.htaccess` is enabled by default. To disable `.htaccess`, you can remove the following contents from `Dockerfile`

	# config to enable .htaccess
    ADD apache_default /etc/apache2/sites-available/000-default.conf
    RUN a2enmod rewrite
