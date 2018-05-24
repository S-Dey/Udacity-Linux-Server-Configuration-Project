# Udacity - Linux Server Configuration Project

An Udacity Full Stack Web Developer II Nanodegree Project.

## About

This tutorial will guide you through the steps to take a baseline installation of a Linux server and prepare it to host your Web applications. You will then secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing Flask-based Web applications onto it.

In this project, I have set up an Ubuntu 18.04 image on a DigitalOcean droplet. The technical details of the server as well as the steps that I have taken to set it up can be found in the succeeding sections.

### Technical Information About the Project

- **Server IP Address:** 206.189.151.124
- **SSH server access port:** 2200
- **SSH login username:** grader
- **Application URL:** http://206.189.151.124.xip.io

## Steps to Set up the Server

### 1. Create the RSA Key Pair

On your local machine, you will first have to set up the public and private key pair. This key pair will be later used while securely logging in with SSH. While the private key will be kept with you in your local machine, the public key will be stored in the server.

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

### 2. Set up a Droplet on DigitalOcean and Add SSH Keys

1. Log in or create an account on [DigtalOcean](https://cloud.digitalocean.com/login).

2. Go to the Dashboard, and click "Create Droplet".  

3. Choose "Ubuntu 18.04 x64" image from the list of given images.

4. Choose a preferred size. In this project, I have chosen 1GB/1 vCPU/25GB configuration.

5. In "Add Your SSH Keys" section, paste the content of your public key, `udacity_project.pub`:
   
   ![Add SSH Keys image](https://res.cloudinary.com/sdey96/image/upload/v1527149812/ssh_jhd3zp.png)
  
   This step will automatically add the following rule in the `/etc/ssh/sshd_config` file:
   
   ```
   PasswordAuthentication no
   ```
   
   This rule will essentially disable password authentication on root user, and rather would use SSH only to login. 
