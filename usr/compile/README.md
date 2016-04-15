#Compiling new versions
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
- Commit and push the repo of your application.
- SSH into you application and run the following commands:
```BASH
cd ${OPENSHIFT_REPO_DIR}/php/compile
./libs
./php
./libs_package
```
Once compiling is done you can download the files `php-{version}.tar.gz` and `libs.tar.gz` from you application.
