---
layout: post
title:  "Docker on Windows"
date:   2016-10-20 21:56:15 -0700
categories: windows docker
---
This goes over my experience with Docker on Windows:


* Installing Docker on Windows 
* Building Docker images
* Running Docker containers (these are instances of Docker images)

## Installing Docker
There is a pretty straight forward guide for installing docker on windows, [go here](https://docs.docker.com/engine/installation/windows/).  I went the Docker Toolbox route because I have Windows 10 Home Edition.  During the installation process I already had VirtualBox and Git installed so I left these unchecked (install Kitematic as you will need it later).

## Building Docker Images
After installing, run the Docker Quickstart Terminal which is a bash terminal.
From this point on I followed the instructions [here](https://github.com/noelbk/battlesnake-heroku) to build docker images (I started at Step 2).  The build process didn't seem to like this line from the Dockerfile.

    RUN apt-get update && apt-get -y install $(cat requirements.apt)

I had to clone the repository to my computer and edit that line.  

    RUN apt-get update && apt-get -y install make python python-pip python-dev ncurses-dev mongodb heroku curl tcpdump

Then I built it locally (changing the url https://github.com/noelbk/battlesnake-heroku to the cloned copy on my computer).

    docker build -t battlesnake-python /path/to/local/folder

Repeat this same process with the other build mentioned in Step 3 [here](https://github.com/noelbk/battlesnake-heroku).
So cloning the repo and then changing the Dockerfile so that the line includes everything in requirements.apt, like below. 

    RUN apt-get update && apt-get -y install make python python-pip python-dev ncurses-dev mongodb heroku curl tcpdump


## Clean Up
If something fails during the build process then you are left with these broken containers and images.
This shows broken containers.

    docker ps --filter "status=exited"

This shows broken images.

    docker images --filter "dangling=true"

To delete broken containers and then broken images run the following commands.

    docker rm $(docker ps -q --filter "status=exited")
    docker rmi $(docker images -q --filter "dangling=true")

## Running Docker Container 
Continue on from Step 4 [here](https://github.com/noelbk/battlesnake-heroku).  It is not immediately apparent how to access the actual server web page ... the IP that is listed after running the command on Step 5 is not what you will navigate to.  To find the correct IP, open the Kitematic program that was installed alongside docker (if you didn't check it in the original install then you might have to go back and reinstall it).  Once Kitematic is running look in the left hand column under Containers, you should see the container that you are running.  Click on the container and then select Settings on the right.  Once in Settings click the Ports tab.  Add the port 5000 under Docker Port column and then 5000 after the IP listed in the Mac IP:PORT column.
So for instance this is what I entered in mine.

    192.168.99.100:5000

To navigate to the battlesnake server webpage you type the IP followed by the port then /play

    192.168.99.100:5000/play

Now that you can navigate to the server webpage, start running some snake containers.  This is following Step 6 and 7 from [here](https://github.com/noelbk/battlesnake-heroku).  The IP's listed in Step 7 won't work either so we have to change their ports as well from within Kitematic.  For each container enter the Docker Port as 5000 but add the Mac IP:PORT in increments of 1 starting at 5001.  So for the first snake the Mac IP:PORT column would look something like this.

    192.168.99.100:5001

Now you can create a game with your running snakes.  Navigate to the new page on the battlesnake server by appending /new or click the new button in the top right hand corner.  The url for me was.

    192.168.99.100:5000/play/new


This is as far as I got as I realized the server workers had crashed ...
This is the error.

    OSError: [Errno 2] No such file or directory


I will hopefully find out what the issue is and post about the solution.
