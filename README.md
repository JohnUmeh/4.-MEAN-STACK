# 4.MEAN STACK
IMPLEMENTING MEAN STACK TO UBUNTU ON AWS


# **CREATE AN EC2 INSTANCE**

On aws, create an ec2 instance and save the keypair as a .pem file

![ec2instance](https://user-images.githubusercontent.com/77943759/221347451-5a9fdc7e-0180-4c87-bfbb-1d7baacdc0b4.png)

Open Terminal on your machine and change directory to the Downloads folder with `cd Downloads` where the .pem downloaded file is

Connect to the instance. Run

ssh -i <private_keyfile.pem> username@ip-address

# **INSTALL NODE JS**

Nodejs is a javascript runtime which we will be using in this project  to set up the Express routes and AngularJS controllers.

Run ubuntu update

`sudo apt update`

Run upgrade

`sudo apt upgrade`

![update upgrade](https://user-images.githubusercontent.com/77943759/221362496-ca4551a0-4561-4fb3-9ce2-f0eaec1b715f.png)


Add certificates

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

Install nodejs

`sudo apt install -y nodejs`

![installnodejs](https://user-images.githubusercontent.com/77943759/221362454-04df6a70-e106-464b-9301-6389342c3698.png)


# **INSTALL MONGODB**

for this app, we need a databse. we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

Run `sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

Next, run `echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

Install MongoDB

`sudo apt install -y mongodb`












