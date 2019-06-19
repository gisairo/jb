+++
title = "Deploying laravel apps to azure"
description = "This is my first attempt at deploying a laravel application to azure"
date = 2019-06-19T03:13:50Z
author = "James Brian"
+++

# Deploying a laravel app to azure the short version

+ Create your local datbase

		CREATE DATABASE sampledb;


+ Clone this repo, I'll explain later

		git clone https://github.com/Azure-Samples/laravel-tasks

+ In your app make sure you do the usual, migrations, keygeneration and start it
	
		php artisan migrate
		php artisan key:generate
		php artisan serve

+ Goto https:portal.azure.com and create a free account the login and go to cloud shell
	+ It looks like a shell shortcut next to the portal settings at the top right 

+ Create a resource group - think computer after final assembly, can host multiple apps, collection of web apps, databases and storage

		az group create --name myResourceGroup --location "West Europe"
	+ Feel free to change the region to your choice, the command below does that

			az appservice list-locations --sku B1 --linux-workers-enabled

+ Create a MySQL server - replace the variables in angulary brackets, including the brackets with your own, 
	
		az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1

+ Configure firewall to allow apps within azure to talk so ip is 0.0.0.0, not healthy, your attacker on azure can easy reach you, lol

		az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
	
	+ Get your ip, google can tell you this, the run this command to give yourself access to the database, replace the '<\your-ip-address\>'

			az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>

	+ Login to your server via your favorite local terminal application

			mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
	
	+ Create a database in yuor mysql server that you have logged into successful using the credentials you set

			CREATE DATABASE sampledb;
			CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
			GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';

+ In your laravel app create an .env.production file and edit the values to match your own

		APP_ENV=production
		APP_DEBUG=true
		APP_KEY=SomeRandomString

		DB_CONNECTION=mysql
		DB_HOST=<mysql-server-name>.mysql.database.azure.com
		DB_DATABASE=sampledb
		DB_USERNAME=phpappuser@<mysql-server-name>
		DB_PASSWORD=MySQLAzure2017
		MYSQL_SSL=true

+ In your config/database.php file edit the mysql array option in the connections.mysql

		'mysql' => [
		    ...
		    'sslmode' => env('DB_SSLMODE', 'prefer'),
		    'options' => (env('MYSQL_SSL')) ? [
		        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
		    ] : []
		],

+ Push your database changes to production
	
		php artisan migrate --env=production --force

+ Generate your production keys
	
		php artisan key:generate --env=production --force

+ Back on azure console, create your deployment user, basically think of it as repo owner on azure repo. You will authenticate with these details with deploying 

		az webapp deployment user set --user-name <username> --password <password>

+ Create an App Service plan - sku B1 is the tier of the resource you are using, obviously on Linux

		az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux

+ Create a web app in the myAppServicePlan App service plan lol, I know

		az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.0" --deployment-local-git
		
+ Configure database settings

		az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"

+	Get an application key to put in your laravel app

		php artisan key:generate --show 

+ Set this to your app in the in azure console 

		az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"

+ Push to Azure from Git 

		git remote add azure <paste_copied_url_here>
		git push azure master

+ The app is autodeployed because of this 3 scripts

		.deployment - This file tells App Service to run bash deploy.sh as the custom deployment script.
		deploy.sh - The custom deployment script. If you review the file, you will see that it runs php composer.phar install after npm install.
		composer.phar - The Composer package manager. 


Thats it, updates are now just a git push away

Read up on streaming the logs and deleting the app and stuff here 
[Source](https://docs.microsoft.com/en-us/azure/app-service/containers/tutorial-php-mysql-app)




	

