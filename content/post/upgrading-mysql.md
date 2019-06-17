+++
title = "Upgrading mysql"
description = "I recently had to update mysql5.6 to 5.7 ."
date = 2017-06-17T03:13:50Z
author = "James Brian"
+++


# Upgrading Mysql 5.6 to Mysql 5.7 in Oracle Linux 7 - Fancy name for Centos


	hr

	service mysql stop

	cp /var/lib/mysql /var/lib/mysql.original

	wget http://repo.mysql.com/mysql57-community-release-el7.rpm -P /tmp/

	yum remove mysql-community-release

	rpm -ivh /tmp/mysql57-community-release-el7.rpm //***tmp can be any folder****/

	yum update mysql




Do yourself a fover and run the final command 


	service mysql start //use --skip-grant-tables if the command below doesn't run
	mysql_upgrade


You might get permission issues with root account, so stop your mysql, and start with the skipgranttables option.

[Source](https://plenium.wordpress.com/2018/07/01/upgrade-mysql-5-6-to-5-7-on-centos-7/)