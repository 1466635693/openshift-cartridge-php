# Openshift Plugin Cartridge for PHP 7

This cartridge is based on [boekkooi's Openshift PHP cartridge](https://github.com/boekkooi/openshift-cartridge-php). Thanks!

PHP-FPM support is enabled.

Fast-CGI support is enabled & CGI support is disabled. 

MYSQLND support is enabled to support PHP 7.

Currently this cartridge works well [jogolor's NGINX cartridge](https://github.com/jogolor/openshift-cartridge-nginx), which is from [boekkooi's Openshift Nginx cartridge](https://github.com/boekkooi/openshift-cartridge-nginx).

You can add this cartridge to your application using:
```BASH
rhc cartridge add -a myapp http://cartreflect-claytondev.rhcloud.com/github/jogolor/openshift-cartridge-php
```
This will add the latest PHP 7.x.x version to your application.

If you want to install a specific PHP version you can add `--env OPENSHIFT_PHP_VERSION=<version>` to the command. For example to install PHP 7.0.7 you can use:
```BASH
rhc cartridge add -a myapp --env OPENSHIFT_PHP_VERSION=7.0.7 http://cartreflect-claytondev.rhcloud.com/github/jogolor/openshift-cartridge-php
```

## Versions
Currently this cartridge has the following versions:
- PHP 7.0.7

## Configuration
- Pass PHP scripts to PHP-FPM

Edit your `.openshift/nginx.conf.erb` and add the following lines within the `server` section:
```
# Pass PHP scripts to PHP-FPM
location ~ \.php$ {
    fastcgi_pass unix:<%= ENV['OPENSHIFT_PHP_SOCKET'] %>;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param PATH_INFO $fastcgi_script_name;
    include <%= ENV['OPENSHIFT_NGINX_DIR'] %>/usr/nginx-<%= ENV['OPENSHIFT_NGINX_VERSION'] %>/conf/fastcgi_params;
}
```

- Customize PHP configuration

In your application create the following directories:
```
.openshift/cli/
.openshift/fpm/
```
SSH into your application and copy original PHP configuration files as you need by running the following commands:
```BASH
cp ${OPENSHIFT_PHP_DIR}/conf/php.ini.erb ${OPENSHIFT_REPO_DIR}/.openshift/cli/
cp ${OPENSHIFT_PHP_DIR}/conf/php-fpm*.erb ${OPENSHIFT_REPO_DIR}/.openshift/fpm/
```
Now you can customize your own configuration files in `.openshift/cli` and `.openshift/fpm` directories.

## Composer/PECL extensions
- Composer

Composer is installed by default and can be used by simply ssh into your application and typing in `composer`.

- PECL extensions

To auto install PECL extensions, you can create `.openshift/php-pecl.txt` with a pecl extension name and version per line. For example:
```
apcu 5.1.3
mongodb 1.1.6
```
- Update Composer & install PECL extensions

After creating `.openshift/php-pecl.txt`, SSH into your application and run the following commands:
```BASH
${OPENSHIFT_PHP_DIR}/bin/control build
${OPENSHIFT_PHP_DIR}/bin/control start
```
The first command will update your PHP configuration files, install PECL extensions in `.openshift/php-pecl.txt`, and update Composer.
The second command will start your PHP cartridge.

## Compiling new versions
- Create  an Openshift application using:
```BASH
rhc create-app nginx http://cartreflect-claytondev.rhcloud.com/github/jogolor/openshift-cartridge-nginx
```
- Clone your application.
- Create a folder `php` in the cloned repo of your application.
- Clone this plugin cartridge's repo into the folder `openshift-cartridge-php` and copy its folder `usr/compile` into the folder `php` you have just created.
- Set the versions you need to compile in `php/compile/versions`.
- Optional: Modify the versions of the dependent liberaries  as you need in `php/compile/libs`.
- Optional: Modify the configuration arguments as you need in `php/compile/php`.

Note: FILEINFO support should be disabled to compile PHP 7 on Openshift SMALL gears.
- Commit and push the repo of your application.
- SSH into you application and run the following commands:
```BASH
cd ${OPENSHIFT_REPO_DIR}/php/compile
./libs
./php
./libs_package
```
Once compiling is done you can download the files `php-{version}.tar.gz` and `libs.tar.gz` from you application.
- Extract `php-{version}` from `php-{version}.tar.gz` and place it into the folder `openshift-cartridge-php/usr`.
- Optional: If you modified `php/compile/libs`or `php/compile/php`, extract `compile` from `php-{version}.tar.gz` and place it into the folder `openshift-cartridge-php/usr`.
- Optional: If you modified `php/compile/libs`, extract `libs` from `libs.tar.gz` and place it into the folder `openshift-cartridge-php/usr/shared`.
- Delete the folder(s) `openshift-cartridge-php/usr/php-{version}` you do not need any more.
- Modify the value of `PHP_VERSION` as you need in `openshift-cartridge-php/bin/setup` and `openshift-cartridge-php/bin/install`.
- Edit `openshift-cartridge-php/metadata/manifest.yml` as you need.
- Commit and push this new repo to your own `openshift-cartridge-php` repo on `github.com`.
- Add your new cartridge to your application using:
```BASH
rhc cartridge add -a myapp http://cartreflect-claytondev.rhcloud.com/github/<user>/openshift-cartridge-php
```
This will add your new cartridge to your application with the php version you have set in `openshift-cartridge-php/bin/setup` and `openshift-cartridge-php/bin/install`.
If you want to add a specific php version you can add `--env OPENSHIFT_PHP_VERSION=<version>` to the command:
```BASH
rhc cartridge add -a myapp --env OPENSHIFT_PHP_VERSION=<version> http://cartreflect-claytondev.rhcloud.com/github/<user>/openshift-cartridge-php
```

## Update Cartridge
To update the latest cartridge you can do the following:
```BASH
rhc cartridge remove -a myapp --confirm  php
rhc cartridge add -a myapp http://cartreflect-claytondev.rhcloud.com/github/jogolor/openshift-cartridge-php
```
This will remove the old version and install the latest version. If these fail, try again adding `--env OPENSHIFT_PHP_VERSION=<version>` to the command. For example:
```BASH
rhc cartridge add -a myapp --env OPENSHIFT_PHP_VERSION=7.0.7 http://cartreflect-claytondev.rhcloud.com/github/jogolor/openshift-cartridge-php
```
