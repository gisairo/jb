+++
title = "How to enable ssh2 on laragon on windows"
description = "This is a brief on how to install and enable php-ssh2 into laragon on windows 10"
date = 2019-09-04T11:25:50Z
author = "James Brian"
+++

## How to install and enable php ssh2 into laragon on windows 10 64 bit

+ Download php-ssh2 dll here [phpssh2](https://pecl.php.net/package/ssh2/1.1.2/windows) choosing the 64 bit thread safe binary zip file 
+ Unzip php_ssh2-1.1.2-7.2-ts-vc15-x64.zip and move the files php_ssh2.dll and php_ssh2.pdb into the dll files folder that laragon uses e.g 'C:\laragon\bin\php\php-7.2.11-Win32-VC15-x64\ext' 
+ Stop laragon services
+ Enable the php ssh2 extension on largaon by right clicking on laragon then going to php-> Extensions -> ssh2 
+ Start/Restart apache2 and mysql services after
+ Check that ssh2 extension is enabled via phpinfo 
    * You can also run the following command 
        - 

                php -i | grep ssh

             in your favourite terminal emulation program that has access to the laragon php version you installed. 
        - It should return 
                *   

                        Registered PHP Streams => php, file, glob, data, http, ftp, zip, compress.zlib, https, ftps, ssh2.shell, ssh2.exec, ssh2.tunnel, ssh2.scp, ssh2.sftp, phar
                        libSSH Version => libssh2/1.8.0
                        ssh2
                        libssh2 version => 1.8.0
                        banner => SSH-2.0-libssh2_1.8.0
                        PLINK_PROTOCOL => ssh
                        SVN_SSH => C:\\Program Files\\Git\\bin\\ssh.exe
                        $_SERVER['PLINK_PROTOCOL'] => ssh
                        $_SERVER['SVN_SSH'] => C:\\Program Files\\Git\\bin\\ssh.exe
            
                
