## Create an EC2 instance
**EC2** is one of AWS' _*compute*_ services. That means it has the brains and resources to run your code.

- From the AWS Console, select EC2 and click 'Launch Instance'
- Select the top, Amazon Linux 2 AMI
- For now, the default setting on all the setup steps are okay, the t2.micro will be suitable for most small applications
- Once you get to the overview, hit Launch!
- In the next modal, select 'Create a new key pair', give it any name you like eg. `keypair`. Download and keep it somewhere safe!
- Hit 'Launch Instance'!
- Click on 'View Instances' in the bottom right to see your new instance. It may take a minute to get to the `Running` state.

## Access your instance
### Connect in the browser
You can interact with your instance **in the browser** by clicking on the instance ID > Connect > Connect. This will open a tab with a terminal connected to your instance.

### Connect via SSH
You can find your instance's public IP address by clicking on the instance ID from the Instances overview page. \
For the user, the standard user name for the following AMIs are: \
Debian: _admin_ \
RedHat: _ec2-user_ \
Ubuntu: _ubuntu_

#### Linux/MacOS
In your local terminal application of choice, run: \
`ssh -i <path-to-your-keypair> <user>@<ip>` \
eg: `ssh -i ./keypair.pem ec2-user@54.188.152.32`

If a connection is refused, run `chmod 600 <path-to-your-keypair.pem>` and try again

#### Windows
If you want to do connect **via SSH** and your local machine is running Windows, you will need to make sure you have an SSH client installed such as [PuTTY](https://www.putty.org/) (other OS should ship with an SSH client pre-installed.) If using PuTTY, open PuTTYgen and load the PEM key you downloaded when creating your instance. Click `Save private key` and store somewhere safe.

To connect:
- Open PuTTY and insert the instance's public IP Address in the Host Name field
- Navigate to Connection > SSH > Auth in the left pane and then select the downloaded private key in PPK format
- Login as ec2-user and you will see the EC2 server welcome banner and be placed in the Linux shell:

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
