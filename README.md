# Workshop
# "Continuous Integration with Jenkins and Shippable"
## Installation
- Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
- Install Vagrant: https://www.vagrantup.com/downloads.html
- For Windows machines only - run in the terminal:
	```
	vagrant plugin install vagrant-vbguest
	```
- Clone or download tutorial git repository https://github.com/omelkova/ci_workshop.git
- Open Terminal and change directory to `box` folder and run Vagrant box:
```sh
cd box
vagrant up
```
- After installation will be finished open the browser and type `http://localhost:7070`, you have to see Jenkins start page:
![Jenkins start](https://cloud.githubusercontent.com/assets/9073171/23781566/e81a465e-0556-11e7-9120-485b89965bd2.png)
- In order to get the password log in to the VM and navigate to open mentioned file:
```sh
vagrant ssh
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Copy-paste the password

- Customize Jenkins: select "Select plugins to install". Make sure to select following plugins:
  * Gradle Plugin
  * JUnit Plugin
  * Bitbucket plugin

press "Install"
![Plugin installation](https://cloud.githubusercontent.com/assets/9073171/23782332/2c4d145a-055c-11e7-8578-9659d90f6a26.png)
- Create admin user as prompted:
![Create user](https://cloud.githubusercontent.com/assets/9073171/23782364/7c6a250e-055c-11e7-9a04-94b961fc8c10.png)
- Start using Jenkins! Setup is finished!

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
./gradlew assemble
./gradlew test
```
If you have forked the workshop project - one test should fail.
### Creating a job for Bitbucket project
Create a new job as before.
In the "Source Code Management" section select "Git", enter the URL of your forked project. In the Credentials section click 'Add', add your Bitbucket username and password, click "Add":
![BB credentials](https://cloud.githubusercontent.com/assets/9073171/23786978/792eadb2-0578-11e7-93f7-b32223747824.png)
Add created credentials:
![Add credentials](https://cloud.githubusercontent.com/assets/9073171/23787027/b3a85826-0578-11e7-9538-17f586c44604.png)
In the 'Build Triggers' section select the 'Build when a change is pushed to BitBucket' option:
![build when change pushed](https://cloud.githubusercontent.com/assets/9073171/23788943/7e66f344-0581-11e7-818a-62623ebc472e.png)
In the 'Build' section select 'Use Gradle Wrapper' radio button. Also tick "Make gradlew executable". Also setup a `test` gradle task:
![gradle conf](https://cloud.githubusercontent.com/assets/9073171/23787239/c71c7936-0579-11e7-820d-9b128670931c.png)
Press "Save".
Go to "Bitbucket job" project and click "Build now". See the console output by selecting "Console Output" in the build view.
### Setup webhooks
To be able to run build on every 'git push' we need to get a Jenkins bitbucket-webhook address. To allow Bitbucket to see our Jenkins server we need to make it available in the Internet. For that we will use ngrok utility (https://ngrok.com/)
#### Setup ngrok
  * Go to [ngrok](https://ngrok.com/) web page
  * Download ngrok for your OS
  * unzip to some location
  * open the Terminal in this location and run:
    ```
    ./ngrok http 7070
    ```
Copy the forwarding URL and !!<span style="color:blue">do not close the console</span>!!:
![ngrok](https://cloud.githubusercontent.com/assets/9073171/23788279/7c726c9c-057e-11e7-958e-484ee17fb3fb.png)
After your Jenkins server become available in the Internet go to your Bitbucket project settings, Webhooks section in the left menu, press "Add webhook". Give any name to your new webhook, paste ngrok forwarding URl+/bitbucket-hook/, press "Save":
![bb webhook settings](https://cloud.githubusercontent.com/assets/9073171/23788606/ec7e2e58-057f-11e7-96b1-8be7376e1b70.png)
Note that this webhook become invalid once you will close your terminal with ngrok running.
Once you push changes to your Bitbucket repository the Jenkins build will be triggered automatically.
### Setup Slack status notifier 
  * Login to your Slack account(https://slack.com/)
  * Configure the Jenkins integration: https://my.slack.com/services/new/jenkins-ci
  * To install this plugin on your Jenkins server
    * Go to the main Jenkins page
    * Press `Manage Jenkins` on the left panel
    * Click on `Manage Plugins` and search for Slack in the `Available` tab. Click the checkbox and install the plugin.
![install Slack Notifier](https://cloud.githubusercontent.com/assets/9073171/23962920/4e2da584-09b8-11e7-86e6-f3fafb9e9a7c.png)
    * After it's installed, click on `Manage Jenkins` again in the left navigation, and then go to `Configure System`. Find the Global Slack Notifier Settings section and add the provided values:	
![configure Slack](https://cloud.githubusercontent.com/assets/9073171/23963259/56e0e1a4-09b9-11e7-9175-ea3ca6e8fe29.png)
    * In the project configurations go to the `Post-build Actions` and add `Slack Notifications`, choose events you'd like to be notified about.
![conf Slack](https://cloud.githubusercontent.com/assets/9073171/23963417/e51f581a-09b9-11e7-8feb-d0b38a601eb3.png)

press `Save`. Now you will receive notifications to the selected slack channel during each build.
## Shippable
### Installation
* Sign in to Shippable (http://www.shippable.com/) with your Bitbucket credentials
* Click on the button on the top-left corner of the page and select your Bibucket name
* Click on `Enable project` button
* Find your project and click on `Enable`
![enable shippable](https://cloud.githubusercontent.com/assets/9073171/23790445/bec5411a-0587-11e7-8349-92b9bf8099bc.png)

### Configuration
* Add `shippable.yml` from this tutorial to the root folder of your project
* git add, commit and push. And that is it. Your project's build will be triggered automatically on Shippable and the build status will be displaied on Bitbucket `Build` field.
