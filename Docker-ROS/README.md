# intro
In this Guide, we will describe the steps to install a ubuntu22.04 based docker with ROS2 Humble. You can of course make the docker the ROS distribution you wish.

If you work on windows, please be careful with your firewall rules, some ports used by ROS/ROS2 may be blocked.

# Docker Installation
You can work with Docker Desktop or classic CLI docker.
# Docker desktop install

In order to install Docker Desktop, you can follow those steps:

1. Go on the official Docker website : https://www.docker.com/products/docker-desktop
2. Click on "Get Started for Free".
3. You may need to create a docker account.
4. Download docker desktop and follow the installation instructions.


Once finished, you're ready to go

# Docker no gui

You can find more detailed steps on the official docker website: https://docs.docker.com/engine/install/ubuntu/

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```



# DockerFile
A DockerFile is a file that contains all the necessary instructions to create a Docker image. It describes the environment you want to work in, and you will be able to replicate in the future (what's pretty useful). 
This is the DockerFile is made for the ubuntu22 version we need in order to install ROS2 Humble next.  

```docker
#change the version if needed
FROM ubuntu:22.04 
ARG DEBIAN_FRONTEND=noninteractive

USER root

RUN apt-get update
  

RUN apt-get install -y build-essential sudo terminator iproute2 gedit lsb-release lsb-core wget nano

RUN adduser user
RUN adduser user sudo
#remove password
RUN passwd -d user

USER user
```

# Create the image from the DockerFile

For this step, I recommand to work with Visual Studio Code and install the "Docker" extension on it.
Then you can right click on your DockerFile and symply choose **Build image...**  

![buildimagevscode](https://github.com/R1leMargoulin/Guides/assets/73824807/a42c6960-36a4-4f6d-a614-bbb57a9d5512)

Then, you need to give a name to your image, I'll call it **"ros2-humble-img"**

# From Image to Container
Open a terminal and type the following command. (Don't forget to change the name of the image you created (**ros2-humble-img** for me here) and the name of the container you want (**ros2-humble** for me).  

```Bash
docker run --name ros2-humble -it -v "C:\\:/mnt/c" --network hostros2-humble-img bash
```

![docker_desktop_ros](https://github.com/R1leMargoulin/Guides/assets/73824807/cdf9e2d2-364e-43ca-bb70-857d79d2f383)


# GUI on the Docker with Xming
Install Xming : [Xming X Server for Windows](https://sourceforge.net/projects/xming/)

then launch X-launch.

then go in the Docker Terminal, run this command:
```bash
export DISPLAY=host.docker.internal:0
```
I advise to put this command into the ~/.bashrc file in order to have it by default (and avoid retyping it every time).

Then launch terminator with the command:
```bash
terminator -u&
```

You are now ready to go, let's install ROS2!

# ROS2 install
Use this as a .sh file in order to automatically install ROS2 humble, if you are using an other version, refer to the ros documentaion in order to install it (https://docs.ros.org).
```bash
#!/bin/bash

sudo apt-get install -y build-essential sudo terminator iproute2 gedit lsb-release lsb-core wget nano

locale  # check for UTF-8
sudo apt update 
sudo apt install locales -y
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
locale  # verify settings

sudo apt update

sudo apt install software-properties-common -y
sudo add-apt-repository universe -y

sudo apt update && sudo apt install curl
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

sudo apt update
sudo apt upgrade

sudo apt install iputils-ping -y

sudo apt install ros-humble-desktop

source /opt/ros/humble/setup.bash

sudo apt-get install python3-rosdep
sudo apt install python3-colcon-common-extensions
```

Don't forget to source your ROS
```bash
source /opt/ros/humble/setup.bash
```

you can also add this one to your .bashrc :)


# Exporting your Image
If you want to export your Docker Image after your ROS/ROS2 development or with your fresh ROS/ROS2 installation, use those command lines on your host machine:

```bash
#creating the image
docker commit CONTAINER_NAME_OR_ID NEW_IMG_NAME

#exporting the image in a tar file
docker save NEW_IMG_NAME > PATH_WHERE_YOU_WANNA_SAVE
```
(you can have the container name or id with the **docker ps** command and the image name/id with the **docker images** command.

You are now ready to work with any ROS distribution you want on your machine. No more quadra-boot!

# Outro
Thanks for your reading, I hope this guide will be useful :)  

I'm sure it is not perfect, so please contact me if you see any point of improvement for this Guide!  

Linkedin: https://fr.linkedin.com/in/erwan-martin-76b931199  

mail: erwan.martin666@gmail.com/emartin@cesi.fr
