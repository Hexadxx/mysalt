# My Salty Project
This very salty project is for the [Configuration Management Systems course by Tero Karvinen](https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/)

## The purpose

This project is to create a Salt-state by using SaltStack. The purpose of this Salt-state is to install useful command line tools and programs such as these:

  - Git
  - Micro
  - UFW
  - Trash-cli
  
I decided to choose these programs and tools as I found them useful during my time using Linux with the exception of Trash-Cli but I included it to the list due the recommendation of using the program during this course so I decided to include it in this project.  

## Environment

Host Machine: 
  
  - OS Name	Microsoft Windows 11 Pro
  - Version	10.0.22621 Build 22621
  - Processor	AMD Ryzen 5 5600X 6-Core Processor, 4601 Mhz, 6 Core(s), 12 Logical Processor(s)
  - Installed Physical Memory (RAM)	32.0 GB

Vagrant v2.3.3(To create and manage virtual machines) 
VirtualBox: Version 7.0.4 r154605 (Qt5.15.2)

## 1. Starting the project

So I started the project by creating a master and minion virtual machines using Vagrant. For Vagrant it is suggested to be used your main computer instead of a virtual machine so I used my own computer. The Vagrant was already installed on my computer previously(like [this](https://github.com/Hexadxx/servermanagmentcourse#second-assignment-a-2)) so I moved to the next step which was to actually create the master and slave machines.

## 2. Creating the machines
(for this part I used this [guide](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/?fromSearch=vagrant))
So to create these machines I first created a directory on my computer where using it would not cause any issues. so I navigated to my user directory and created a folder called "project" 

![ksI05Nd](https://user-images.githubusercontent.com/77589513/207695298-c76ee826-cfc9-4358-b7c6-f5735c04b8e3.png)

then created a file called "Vagrantfile" and 

pasted the vagrantfile contents to my file but instead of "t001" and "t002" I renamed them to "master" and "slave". saved and ran the command "vagrant up" on the powershell

## 3. Testing the machines

So after the machines finished installing it created these machines as shown:

![HgxfoVt](https://user-images.githubusercontent.com/77589513/207696535-8cf66835-5671-4873-af5a-46eafa92152e.png)

## 4. Updating the machines and installing Salt

Next I connected to the machines with the command

> vagrant ssh "machine name"

I then did all the updates and upgrades with the commands: sudo apt-get update, sudo apt-get upgrade -y, sudo apt update and sudo apt upgrade -y. After updating and upgrading the machines I installed salt-master on the machine master and salt-minion on slave machine with the commands:

> sudo apt-get install salt-master
> sudo apt-get install salt-minion

## 5. Connecting the slave to the master

First I picked up the host ip which in this case is "192.168.88.101" and edited the minion file with the command

> sudoedit /etc/salt/minion 

and uncommented the "master:" and "id:" and on the put the ip of the master in the master part and added slave on the id (the id basically is used to name your slaves). Next went back to the master and accepted the slave with the command:

> sudo salt-key -A
  
I realized I forgot to restart the service so the changes to the minion file are actually recognized so went back to slave and restarted the service with the command:

> sudo systemctl restart salt-minion

went back to master ran the salt-key command and it worked as shown below:

![mzFZmGk](https://user-images.githubusercontent.com/77589513/207716471-e7cc91c7-0966-4db2-b2c2-b26a75c262b8.png)

## 6. Testing to make sure salt is working properly

So I do this test by first going to master and creating a directory for the salt states

> sudo mkdir /srv/salt

then create a helloworld.sls in there(state file)

> sudoedit helloworld.sls

ran the command

> sudo salt '*' state.apply helloworld

But it gave me an error:

![euoi6E1](https://user-images.githubusercontent.com/77589513/207721375-eacf33bc-9093-496b-84f6-af3b0ccc7eeb.png)

and realized I needed to have the helloworld.txt in the place it is mentioned in the source part so in this case it has to be located in the same folder as the state file. So I created the helloworld.txt file and it worked:

![GYg8qPH](https://user-images.githubusercontent.com/77589513/207722035-23361fde-9222-4fae-b20e-480818a96cf1.png)

## 7. Creating our own state

So now I started creating the state that installs the programms mentioned before.
I created a mew directory called basestate inside /srv/salt/
Next I created a new state file called init.sls inside the basestate directory.
and added this into the file:

![1jJmD1S](https://user-images.githubusercontent.com/77589513/207724032-4c51a4d4-308b-4b0e-95f2-52652ec53a1d.png)

## 8. Testing our own state

So finally the moment of truth, to see if our own state actually works I ran the command:

> sudo salt '*' state.apply basestate

And so it seems that it does work!

![MJZgk5J](https://user-images.githubusercontent.com/77589513/207724830-d7c394ae-e84c-47cc-b411-9b1381445f08.png)

![9VwDY5x](https://user-images.githubusercontent.com/77589513/207724845-dd883016-272c-41af-b711-47ff9641c4b0.png)

And to go and test it out I go to my slave machine I run every program that was supposed to be installed to see if they actually succesfully installed.
And everything worked perfectly I realized when testing trash-cli and ufw u can not just run it just to test it like you have to do something with it so here are how I tested for all these programs to work:

![FmfUh25](https://user-images.githubusercontent.com/77589513/207726013-94849b59-ae55-4fbd-8bde-7e561861ac23.png)

![QFAxAtX](https://user-images.githubusercontent.com/77589513/207726044-d8101f75-f25f-4f77-9442-5527153d6530.png)

So here u notice I actually tested micro by creating a text file which I used to test the trash-cli

![neliNlh](https://user-images.githubusercontent.com/77589513/207726129-747b06e0-cfa1-4692-aee8-0885aa3ec476.png)

And ladies and gentlemen that is how you can possibly install packages in a matter of minutes to possibly 100s or even thousands of slaves.

# Sources

SaltStack 2022. Salt.State.Pkg: https://docs.saltproject.io/en/latest/ref/states/all/salt.states.pkg.html. Visited: 14.12.2022

Tero Karvinen 2017. Vagrant Revisited – Install & Boot New Virtual Machine in 31 seconds: https://terokarvinen.com/2017/04/11/vagrant-revisited-install-boot-new-virtual-machine-in-31-seconds/?fromSearch=vagrant Accessed: 14.12.2022

Tero Karvinen 2018. Salt Quickstart - Salt Stack Master and Slace on Ubuntu Linux: https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/. Visited: 14.12.2022

Tero Karvinen 2018. Salt States – I Want My Computers Like This: https://terokarvinen.com/2018/salt-states-i-want-my-computers-like-this/?fromSearch=salt. Visited: 14.12.2022

Tero Karvinen 2021. Two Machine Virtual Network With Debian 11 Bullseye and Vagrant: https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/. Visited: 14.12.2022

Vagrant: https://www.vagrantup.com/ Visited: 8.12.2022

Trash-Cli: https://github.com/andreafrancia/trash-cli Visited 14.12.2022



