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

```bash
 # apt update && apt upgrade
```

### 4. Changing the SSH Port from 22 to 2200

1. Open the `/etc/ssh/sshd_config` file with `nano` (or any other text editor of your choice):

   ```bash
   # nano /etc/ssh/sshd_config
   ```

2. Find the line `#Port 22` (would be located around line 13) and change it to `Port 2200`, and save the file.

3. Restart the SSH server to reflect those changes:
   ```bash
   # service ssh restart
   ```

4. To confirm whether the changes have taken place or not, run:
   ```bash
   # exit
   ```

   This will take you back to your host machine. After you are back to the host machine, run:

   ```bash
   $ ssh root@206.189.151.124 -p 2200
   ```

   The `-p` option explicitly tells what port the SSH server operates on. You should now be able to log in to the server as `root` user.

### 5. Configure Timezone to Use UTC

To configure the timezone to use UTC, run the following command:

```bash
# sudo dpkg-reconfigure tzdata
```

It then shows you a list. Choose ``None of the Above`` and press enter. In the next step, choose ``UTC`` and press enter.

### 6. Setting Up a Basic Firewall

Now we would configure the firewall to allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):

```bash
# ufw allow 2200/tcp
# ufw allow 80/tcp
# ufw allow 123/udp
```

To enable the above created rules, run:

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




### 7. Create the User `grader` and Add it to the `sudo` Group

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

2. Run the following command to add the user `grader` to the `sudo` group to grant it administrative access:

   ```
   # usermod -aG sudo grader
   ```
