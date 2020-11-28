## Create an EC2 instance or ECS service
**EC2** is one of AWS' _*compute*_ services. That means it has the brains and resources to run your code.

**ECS** is AWS' specific container service - it can automatically scale up the number of EC2 instances required depending on the traffic to your app. ECS comes ready with Docker and an agent to handle the automatic scaling logic. If using ECS, we recommend selecting the option to use AWS Fargate as it handles most of the complex orchestration configuration for you.

***

## Prepare your EC2 instance to use Docker (not needed for ECS)
- `sudo yum -y install docker`: install
- `sudo systemctl start docker`: start the Docker daemon
- `sudo docker info`: see the current Docker info

NB: If you don't want to keep type `sudo` in front of each command, you can add yourself to a docker user group
- `sudo groupadd docker`: create the group (it will tell you if it already exists)
- `sudo gpasswd -a $USER docker` add your user to the group
- `newgrp docker`: update the groups
- `docker info`: you should have access without typing `sudo` now. If now, run `sudo systemctl restart docker` and try again

***

## Test run your Docker install
- `docker run hello-world`: this should pull down the hello-world image and run it
If successful, you will see output including
```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

***

## Prepare your instance to use git and clone your repo
- `sudo yum -y install git`: install
- `git clone <your repo>`: clone your code
- `cd` into the cloned folder

***

## Prepare your Dockerfile
If your repo already has a Dockerfile you can skip to the next part!

If not, let's make one in vim:
- `vi Dockerfile`: open a file called Dockerfile in vim
- `i`: enter Insert mode
- type out your Dockerfile
- `Esc`: exit Insert mode
- `:wq`: save and exit vim

You can print out the contents of your Dockerfile with `cat Dockerfile`

***

## Build your Docker image based on this Dockerfile
- `docker build -t <image-name>:<image-tag> .` \
eg. `docker build -t my-app-server:latest .`
- `docker images`: check to see the image has been created

***

## Find your instance's public IP address
You will need this to access your running app so make a note of the output!
- `curl ipecho.net/plain; echo`

If you try visiting this IP address in your browser now, nothing will be returned as nothing is running yet

***

## Run a container and start your server
Depending on your Dockerfile and what you are looking to do, your run command may be a bit different. See our [Docker Cheatsheet](https://github.com/getfutureproof/fp_guides_wiki/wiki/Docker-101-Cheatsheet) for more details.

Revisit the IP address you recorded earlier and if all goes well, you should have access to your app!
