+++
title = "Changing wordpress user password via database"
description = "How to change your wordpress password if you have access to the database"
date = 2019-06-20T00:45:50Z
author = "James Brian"
+++

# Changing your wordpress user password via database

+ Create your local datbase

		UPDATE `wp_users` SET `user_pass`= MD5('yourpassword') WHERE `user_login`='yourusername';

+ You should be able to login your wordpress account using the username and password you just changed above