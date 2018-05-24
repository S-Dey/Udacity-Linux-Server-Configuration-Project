# Udacity - Linux Server Configuration Project

An Udacity Full Stack Web Developer II Nanodegree Project.

## About

This tutorial will guide you through the steps to take a baseline installation of a Linux server and prepare it to host your Web applications. You will then secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing Flask-based Web applications onto it.

In this project, I have set up an Ubuntu 18.04 image on a DigitalOcean droplet. The technical details of the server as well as the steps that I have taken to set it up can be found in the succeeding sections.

### Technical Information About the Project

- **Server IP Address:** 127.0.0.1
- **SSH server access port:** 2200
- **SSH login username:** grader
- **Application URL:** http://127.0.0.1

## Steps to Set up the Server

### 1. Creating Public and Private Key Pair

On your local machine, you'll have to first set up the public and private key pair. While the private key will be kept with you in your local machine, the public key will be stored in the server.

To create public and private key pair, follow the following steps:

1. Open terminal, and run the following command:
   
   ```console
   username@host:~$ ssh-keygen
   ```
