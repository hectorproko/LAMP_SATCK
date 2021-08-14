# IMPLEMENTING LAMP STACK

<!-- 
https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/Connect.png
-->
![Markdown Logo]()

Before we start we need to have an environemnt to work with. I will we using my AWS account to create an EC2 instance with an Ubuntu Server.

* First thing I'm going to do when I log in to AWS is look for the **EC2** services. There are various methods to navigate to it, here I'm using the **search bar** <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/ec2search.png)

* Once you navigate to the **EC2** page look for a **Launch instance** button <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/launchInstance.png)

* You will then be prompted to pick an OS Image. I will be using **Ubuntu Server 20.04 LTS (HVM), SSD Volume**. Once done click **Select**

* I will pick the **t2.micro** instace type <br /> 
 ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/t2micro.png)

* I will leave default settings and click **Review and Launch** <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/reviewLaunch.png)

* As you can see we have a Security Group applied to the instance by default which allows SSH connections <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/sshDefault.png)

* After reviewing you can launch your instancing by clicking <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/launch.png)

* We are prompted to create or use an existing Key Pair. I will be creating a new one. I will use this .pem key to SSH into the instance later on. <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/keyPair.png)

* Once you have downloaded your key launch intance <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/LaunchInstances.png)

* To go to the instances dashboard <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/ViewInstances.png)

* If your instance is up and running you will see something like this <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/Running.png)

* To find information on how to connect click on your **Instance ID**

* In the top-right corner you should see the button **Connect**, click on it <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/Connect.png)

* Look for the **SSH client** tab <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/SSHclient.png)

* Under **Example** you'll find an **ssh** command with eveything you need to connect to the instance from a terminal

**Examaple:**
```bash
ssh -i "daro.io.pem" ubuntu@ec2-3-216-90-84.compute-1.amazonaws.com
```
Make sure when you run the command that your current working directory in the terminal is where your KeyPair/.pem is located because in the above example I'm using a relative path to point to my key

* A successful log-in <br /> 
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/ubunutuLogIn.png)


## INSTALLING APACHE AND UPDATING THE FIREWALL
---

* We will install Apache using Ubuntu's package manager **apt**
    * Update list of packages in package manager
    ```bash
    sudo apt udpate
    ```
    * To install apache2 package
    ```bash
    sudo apt install apache2
    ```
    * Alternatively you can put everything together in one line
    ```bash
    sudo apt update && sudo apt install apache2 -y
    ```
    The **-y** puts yes whenever the systems asks during install

    * To make sure apache is running 
    ```bash
    systemctl status apache2
    ```

* Now we are going to add a rule to our **Security Group** to allow **Inbound** HTTP requests
    * Navigate to your intances dashboard and select your instance by cliking the empty box <br /> 
    ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/checkMark.png)
    * Look for the **Security** tab <br /> 
    ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/security.png)
    * Click on top of your **Security Group**  <br />
    ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/securityGroup.png) <br />
    Keep in mind yours might look different
    * Under the tab **Inbound Rules** click **Edit inbound rules** button <br />
    ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/editInboundRules.png) <br />
    * Click **Add rule** <br />
    ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/addRule.png) 
    * Pick type **HTTP** and source **0.0.0.0/0** meaning all IPs, you can also use drop-down menu **Anywhere-IPv4**
    * Save it <br />
    ![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/SaveRules.png) 

* You should now be able to see Apache's dummy page <br />
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/dummyPage.png) 


## INSTALLING MYSQL
---

* We will again use **apt** to install this software
```bash
sudo apt install mysql-server
```
* When prompted, confirm installation by typing **Y**, and then **ENTER**.
* Once installation is done we will run a script that removes some insecure default settings and lock down access to your database system. To start the interactive script run:
```bash
sudo mysql_secure_installation
```
And follow the wizard like questions
* Will ask if you want to configure the VALIDATE PASSWORD PLUGIN. I'll put yes for an additioanl layer of security <br />
![Markdown Logo](https://raw.githubusercontent.com/hectorproko/LAMP_SATCK/main/images/validate.png) <br />
Keep answering the questions till the end

* When you’re finished, test if you’re able to log in to the MySQL console by typing:
```bash
sudo mysql
```
You should see
```bash
mysql> 
```
* To exit type **exit**
```bash
mysql> exit
Bye
```

## INSTALLING PHP
---

* Using previous method we are going to isntall 3 packages for php
```bash
sudo apt install php libapache2-mod-php php-mysql -y
```
* Once installation is done confirm you indeed have php intalled by checkig the version
```bash
php -v
```
* At this point you have a fully operational **LAMP**

## CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE
---
* Virtual host allows you to have multiple websites located on a single machine and users of the websites will not even notice it.

* Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the **/var/www/html** directory.
We will leave this configuration as is and will add our own directory next next to the default one.
Create the directory for **projectlamp** using **mkdir** command as follows:
```bash
sudo mkdir /var/www/projectlamp
```
* Next, we'll assign ownership of the directory with our current system user:
