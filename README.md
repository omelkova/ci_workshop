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
-- Gradle Plugin
-- JUnit Plugin
-- Bitbucket plugin
-- Bitbucket Build Status Notifier Plugin
