# Workshop
# "Continuous Integration with Jenkins and Shippable"
## Installation
- Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
- Install Vagrant: https://www.vagrantup.com/downloads.html
- Clone or download git repository https://github.com/omelkova/ci_workshop.git
- Open Terminal and change directory to `box` folder and run Vagrant box:
```sh
cd box
vagrant up
```
- After installation will be finished open the browser and type `http://localhost:7070`, you have to see Jenkins start page:
![Jenkins start](https://cloud.githubusercontent.com/assets/9073171/23781566/e81a465e-0556-11e7-9120-485b89965bd2.png)
- In order to get the password log in to the VM and navigate to the open mentioned file:
```sh
vagrant ssh
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Copy-paste the password

- Customize Jenkins: select "Select plugins to install". Make sure to select following plugins:
  * Gradle Plugin
  * JUnit Plugin
  * Bitbucket plugin
  * Bitbucket Build Status Notifier Plugin (this plugin can not be presented in the list of plugins for initial setup, can be installed later)
press "Install"
![Plugin installation](https://cloud.githubusercontent.com/assets/9073171/23782332/2c4d145a-055c-11e7-8578-9659d90f6a26.png)
- Create admin user as prompted:
![Create user](https://cloud.githubusercontent.com/assets/9073171/23782364/7c6a250e-055c-11e7-9a04-94b961fc8c10.png)
- Start using Jenkins! Setup is finished!
** To install missing plugin select " Manage Jenkins" from the left menu, then "Manage Plugins", select "Available" tab, search for `Bitbucket Build Status Notifier Plugin`, select it for installation and press "Download now and install after restart". 
![install plugin](https://cloud.githubusercontent.com/assets/9073171/23782479/6f1cd0b2-055d-11e7-921e-f02c9d8326d0.png)
Toggle "Restart Jenkins when installation is complete and no jobs are running"
## Create new job
