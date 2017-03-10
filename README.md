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
From the main page press `create new jobs` button. Enter the job's name and select 'Freestyle project'. Press 'OK'
![create new job](https://cloud.githubusercontent.com/assets/9073171/23782650/a20fe1fc-055e-11e7-9ba2-f2042db959af.png)
Scroll down to the 'Build' section, select 'Execute shell' from dropdown menu. Type in the simple shell command `echo Hello world`. Press 'Save':
![setup first build](https://cloud.githubusercontent.com/assets/9073171/23782785/a85e725c-055f-11e7-85e5-9ff433d5e7c8.png)
On the main page of our project press 'Build now' button from the left menu:
![Build now](https://cloud.githubusercontent.com/assets/9073171/23782922/7b3eccd0-0560-11e7-8bc1-29259b4d9388.png)
The build will be scheduled and executed. To see the build result navigate to the 'Build History' on the left menu and click on the blue ball. You will see the console output of the first successful build. 
![console output](https://cloud.githubusercontent.com/assets/9073171/23783004/f4c60d52-0560-11e7-8cd7-e16035e62bd2.png)
Now lets fail our first job. For that go to the main page for the 'first job' project and press 'Configure'. Scroll down to the 'Build' section and change script to the command which does not exist, for example `ech Hello world`, press "Apply". 
Run build again by pressing "Build Now" in the "first job" view. The build will fail:
![build failed](https://cloud.githubusercontent.com/assets/9073171/23785487/8ea50ec8-0570-11e7-9552-3f8bc7857b7d.png)

## Create a real job
### Preparation
To setup build for the Point of sail(POS) app, first, we need to recall how to use `gradle`. Clone your project to the local machine.
  * If you have any problems with accessing your code or do not have this project for some reason, you can first `fork` and then `clone` this Bitbucket repository `https://bitbucket.org/omelkova/ci-workshop-pos`:
    * Create a bitbucket account if you don't have one yet and log in.
    * Go to the [Project](https://bitbucket.org/omelkova/ci-workshop-pos)
    * Click "Fork"
    * Choose any name
    * Click "Fork repository"

After cloning the project to the local machine, cd to the project directory and run following gradle commands:
```sh
./gradlew build
./gradlew test
```
If you fork workshop project - one test should fail.
### Creating a job for Bitbucket project
Create a new job as before.
In the "Source Code Management" section select "Git", enter the URL of your forked project. In the Credentials section click 'Add', add your Bitbucket username and password, click "Add":
![BB credentials](https://cloud.githubusercontent.com/assets/9073171/23786978/792eadb2-0578-11e7-93f7-b32223747824.png)
Add created credentials:
![Add credentials](https://cloud.githubusercontent.com/assets/9073171/23787027/b3a85826-0578-11e7-9538-17f586c44604.png)
In the 'Build' section select 'Use Gradle Wrapper' radio button. Also tick "Make gradlew executable". Also setup a `test` gradle task:
![gradle conf](https://cloud.githubusercontent.com/assets/9073171/23787239/c71c7936-0579-11e7-820d-9b128670931c.png)
Press "Save".
Got to "Bitbucket job" project and click "Build now". See the console output by selecting "Console Output" in the build view.
### Setup webhooks and status notifier
To be able to run build on every 'git push' we need to get a Jenkins bitbucket-webhook address. To make Bitbucket to see our Jenkins server we need to make it available in the Internet. For that we will use ngrok utility (https://ngrok.com/)
#### Setup ngrok
  * Go to [ngrok](https://ngrok.com/) web page
  * Download ngrok for your OS
  * unzip to some location
  * open the Terminal in this location and run:
    ```sh
    ./ngrok http 7070
    ```
Copy the forwarding URL and !!<span style="color:blue">do not close the console</span>!!:
![ngrok](https://cloud.githubusercontent.com/assets/9073171/23788279/7c726c9c-057e-11e7-958e-484ee17fb3fb.png)
After your Jenkins server become available in the Internet go to your Bitbucket project settings, Webhooks section in the left menu, press "Add webhook". Give any name to your new webhook, paste ngrok forwarding URl+/bitbucket-hook/, press "Save":
![bb webhook settings](https://cloud.githubusercontent.com/assets/9073171/23788606/ec7e2e58-057f-11e7-96b1-8be7376e1b70.png)
