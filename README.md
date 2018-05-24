# Udacity - Linux Server Configuration Project

An Udacity Full Stack Web Developer II Nanodegree Project.

## About

This tutorial will guide you through the steps to take a baseline installation of a Linux server and prepare it to host your Web applications. You will then secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing Flask-based Web applications onto it.

In this project, I have set up an Ubuntu 18.04 image on a DigitalOcean droplet. The technical details of the server as well as the steps that have been taken to set it up can be found in the succeeding sections.

### Technical Information About the Project

- **Server IP Address:** 206.189.151.124
- **SSH server access port:** 2200
- **SSH login username:** grader
- **Application URL:** http://206.189.151.124.xip.io

## Steps to Set up the Server

### 1. Creating the RSA Key Pair

On your local machine, you will first have to set up the public and private key pair. This key pair will be used to authenticate yourself while securely logging in to the server via SSH. The private key will be kept with you in your local machine, and the public key will be stored in the server.

To generate a key pair, run the following command:

   ```console
   $ ssh-keygen
   ```

When it asks to enter a passphrase, you may either leave it empty or enter some passphrase. A passphrase adds an additional layer of security to prevent unauthorized users from logging in.

The whole process would look like this:

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/subhadeep/.ssh/id_rsa): /home/subhadeep/.ssh/udacity_project
Created directory '/home/subhadeep/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/subhadeep/.ssh/udacity_project.
Your public key has been saved in /home/subhadeep/.ssh/udacity_project.pub.
The key fingerprint is:
SHA256:JnpV8vpIZqstoTm8aaDAqVmGSF/tGhtDfXAfL2fs/U8 subhadeep@subhadeep-VirtualBox
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|       . . .     |
|      o + o +    |
| .   o o = o =   |
|+.o o o S . = .  |
|+o+. =.= .   . . |
|o= o.oB.=       E|
|+   *=.= +     ..|
|   .oo.o+ .     o|
+----[SHA256]-----+
```

You now have a public and private key that you can use to authenticate. The public key is called `udacity_project.pub` and the corresponding private key is called `udacity_project`. The key pair is stored inside the `~/.ssh/` directory.

### 2. Setting Up a DigitalOcean Droplet

1. Log in or create an account on [DigtalOcean](https://cloud.digitalocean.com/login).

2. Go to the Dashboard, and click "Create Droplet".

3. Choose *Ubuntu 18.04 x64* image from the list of given images.

4. Choose a preferred size. In this project, I have chosen the *1GB/1 vCPU/25GB* configuration.

5. In the section *Add Your SSH Keys*, paste the content of your public key, `udacity_project.pub`:

   ![Add SSH Keys image](https://res.cloudinary.com/sdey96/image/upload/v1527149812/ssh_jhd3zp.png)

   This step will automatically create the file `~/.ssh/authorized_keys` with appropriate permissions and add your public key to it. It would also add the following rule in the `/etc/ssh/sshd_config` file:

   ```
   PasswordAuthentication no
   ```

   This rule essentially disables password authentication on the `root` user, and rather allows SSH logins only.

 6. Click *Create* to create the droplet. This will take some time to complete. After the droplet has been created successfully, a public IP address will be assigned. In this project, the public IPv4 address that I have been assigned is `206.189.151.124`.

 ### 3. Logging In as `root` via SSH

 As the droplet has been created, you can now log in to the server as `root` user by running the following command in your host machine:

 ```
    $ ssh root@206.189.151.124
 ```

 This will look for the private key in your local machine and log you in automatically if the private key is found. After you are logged in, you might see something similar to this:

 ![Root login](https://res.cloudinary.com/sdey96/image/upload/v1527151721/terminal_msihzb.png)

 Now run the following command to update the virtual server:

```
 # apt update && apt upgrade
```

### 4. Changing the SSH Port from 22 to 2200

1. Open the `/etc/ssh/sshd_config` file with `nano` (or any other text editor of your choice):

   ```
   # nano /etc/ssh/sshd_config
   ```

2. Find the line `#Port 22` (would be located around line 13) and change it to `Port 2200`, and save the file.

3. Restart the SSH server to reflect those changes:
   ```
   # service ssh restart
   ```

4. To confirm whether the changes have taken place or not, run:
   ```
   # exit
   ```

   This will take you back to your host machine. After you are back to the host machine, run:

   ```
   $ ssh root@206.189.151.124 -p 2200
   ```

   The `-p` option explicitly tells what port the SSH server operates on. You should now be able to log in to the server as `root` user.

### 5. Configure Timezone to Use UTC

To configure the timezone to use UTC, run the following command:

```
# sudo dpkg-reconfigure tzdata
```

It then shows you a list. Choose ``None of the Above`` and press enter. In the next step, choose ``UTC`` and press enter.

You should now see an output like this:

```
Current default time zone: 'Etc/UTC'
Local time is now:      Thu May 24 11:04:59 UTC 2018.
Universal Time is now:  Thu May 24 11:04:59 UTC 2018.
```

### 6. Setting Up the Firewall

Now we would configure the firewall to allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):

```
# ufw allow 2200/tcp
# ufw allow 80/tcp
# ufw allow 123/udp
```

To enable the above firewall rules, run:

```
# ufw enable
```

To confirm whether the above rules have been successfully applied or not, run:

```
# ufw status
```

You should see something like this:

```
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
```

### 7. Creating the User `grader` and Adding it to the `sudo` Group

1. While being logged into the virtual server, run the following command and proceed:

   ```
   # adduser grader
   ```

   The output would look like this:

   ```
   Adding user `grader' ...
   Adding new group `grader' (1000) ...
   Adding new user `grader' (1000) with group `grader' ...
   Creating home directory `/home/grader' ...
   Copying files from `/etc/skel' ...
   Enter new UNIX password:
   Retype new UNIX password:
   passwd: password updated successfully
   Changing the user information for grader
   Enter the new value, or press ENTER for the default
	   Full Name []: Grader
	   Room Number []:
	   Work Phone []:
	   Home Phone []:
	   Other []:
   Is the information correct? [Y/n]
   ```

   **Note**: Above, the UNIX password I have entered for the user `grader` is, `root`. 

2. Run the following command to add the user `grader` to the `sudo` group to grant it administrative access:

   ```
   # usermod -aG sudo grader
   ```

### 8. Adding SSH Access to the user `grader`

To allow SSH access to the user `grader`, first log in to the account of the user `grader`:

```
# su - grader
```

You should now see a prompt like this:

```console
grader@ubuntu-s-1vcpu-1gb-sgp1-01:~$ |
```

Now enter the following commands to allow SSH access to the user `grader`:

```
$ mkdir .ssh
$ chmod 700 .ssh
$ cd .ssh/
$ touch authorized_keys
$ chmod 644 authorized_keys
```

Now go back to your local machine and copy the content of the public key file `~/.ssh/udacity_project.pub`.

Paste the public key to the virtual server's `authorized_keys` file using `nano` or any other text editor, and save:

```
$ nano authorized_keys
```

After that, run `exit`. You would now be back to your local machine. To confirm that it worked, run the following command in your local machine:

```console
subhadeep@subhadeep-VirtualBox:~$ ssh grader@206.189.151.124 -p 2200
```

You should now be able to log in as `grader` and would get a prompt to enter commands.

Next, run `exit` to go back to the host machine and proceed to the following step to disable `root` login.

### 9. Disabling Root Login

1. Run the following command to log in as `root` from your host machine:
   ```
   $ ssh root@206.189.151.124 -p 2200
   ```

2. After you are logged in, open the file `/etc/ssh/sshd_config` with `nano`:
   ```
   # nano /etc/ssh/sshd_config
   ```

3. Find the line `PermitRootLogin yes` and change it to `PermitRootLogin no`.

4. Restart the SSH server:
   ```
   # service ssh restart
   ```

5. Terminate the connection:
   ```
   # exit
   ```

6. After you are back to the host machine, when you try to log in as `root`, you should get an error:

   ```console
   subhadeep@subhadeep-VirtualBox:~$ ssh root@206.189.151.124 -p 2200
   root@206.189.151.124: Permission denied (publickey).
   ```

### 10. Installing Apache Web Server

To install the Apache Web Server, run the following command after logging in as `grader` user via SSH:

```
$ sudo apt update
$ sudo apt install apache2
```

To confirm whether it successfully installed or not, enter the URL `http://206.189.151.124` in your Web browser:

If the installation has succeeded, you should see the following Web page:

![Screenshot](https://res.cloudinary.com/sdey96/image/upload/v1527170572/Capture_seeiof.png)

### 11. Installing Python 3.6 and pip3

1. To install Python 3.6, run the following command:

   ```
   $ sudo apt install python3
   ```

2. After the installation of Python 3.6 has succeeded, you'll have to install `pip3` to install certain packages later. To install it, run the following command:

   ```
   $ sudo apt install python3-pip
   ```

   To confirm whether or not it has been successfully installed, run:
   
   ```
   $ pip3 --version
   ```

   You should see something like this if it has been successfully installed:
   
   ```
   pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)
   ```

### 12. Installing and Configuring Git

#### 12.1. Installing Git

To install `git`, run the following command:

```
$ sudo add-apt-repository ppa:git-core/ppa
$ sudo apt update
$ sudo apt install git
```

#### 12.2. Configuring Git

To continue using git, you will have to configure a username and an email:

```
$ git config --global user.name "Subhadeep Dey"

$ git config --global user.email "contact@subhadeepdey.com"
```

### 13. Setting Up Apache to Run the Flask Application

#### 13.1. Installing `mod_wsgi`

The module `mod_wsgi` will allow your Python applications to run from Apache server. To install it, run the following command:
   
```
$ sudo apt install libapache2-mod-wsgi-py3
```

You might then want to run:

```
$ sudo a2enmod wsgi
```

After the installation has succeeded, restart the Apache server:

```
$ sudo service apache2 restart
```

#### 13.2. Configuring Virtual Hosts

1. Change the current working directory to `/var/www/`:

   ```
   $ cd /var/www/
   ```

2. Create a directory called `FlaskApp` and change the working directory to it:

   ```
   $ sudo mkdir FlaskApp
   $ cd FlaskApp/
   ```

3. Clone [this repository](https://github.com/SDey96/Udacity-Item-Catalog-Project/tree/development) as the directory `FlaskApp`:

   ```
   $ sudo git clone https://github.com/SDey96/Udacity-Item-Catalog-Project.git FlaskApp
   ```

4. Move to the newly created directory:

   ```
   $ cd FlaskApp/
   ```

5. Checkout to the `development` branch:

   ```
   $ sudo git checkout development
   ```

   The directory tree should now look like this:

   ```
   FlaskApp
    └── FlaskApp
        ├── LICENSE
        ├── README.md
        ├── __init__.py
        ├── client_secrets.json
        ├── database_setup.py
        ├── fake_db_populator.py
        ├── static
        │   └── style.css
        └── templates
            ├── delete.html
            ├── delete_category.html
            ├── edit_category.html
            ├── index.html
            ├── items.html
            ├── layout.html
            ├── login.html
            ├── new-category.html
            ├── new-item-2.html
            ├── new-item.html
            ├── update-item.html
            └── view-item.html
    ```

## References

[1] <https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04>

[2] <http://terokarvinen.com/2016/deploy-flask-python3-on-apache2-ubuntu>

[3] <https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps>