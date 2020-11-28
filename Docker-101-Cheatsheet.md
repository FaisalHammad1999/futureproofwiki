## Getting started
If you've not yet taken your Docker install for a spin, try it out with: \
`docker run hello-world`

Fancy a new Linux machine? \
`docker run -it ubuntu /bin/bash` \
(exit it with `exit`)

***

## Viewing and deleting images & containers
- `docker images`: see all your images
- `docker rmi <image_id>`: remove an image

- `docker ps`: see all running containers
- `docker ps -a`: see all containers including stopped ones
- `docker rm <container_id>`: remove a container
- `docker system prune`: remove all containers

- `docker system prune -a`: remove all Docker items

***

## Making custom images
### Make a new image with a Dockerfile
```Dockerfile
// in Dockerfile
# give a starting image
FROM node:14

# copy over any files you need
COPY hello.js .

# give a command to run upon the container starting
CMD ["node", "hello.js"]
```
`docker build .`

Name it with `docker build . -t <name>` \
Tag it with `docker build . <name>:<tag>` (defaults to 'latest' if not included)

_NB: The Dockerfile above references a file called `hello` in the same folder as the Dockerfile. If trying this out for yourself, you can use this_
```js
// in hello.js
console.log('Hello, Docker!')
```

***

### Make a new image from a container

Let's say you installed python into that ubuntu container above:
`docker run -it ubuntu /bin/bash` \
`apt update` \
`apt install python` \
`exit`

To make a new image based on this updated container: \
`docker ps -a` - to get container id of stopped ubuntu container \
`docker commit <container_id> <new-image-name>`

Optionally make a change to the run command during the commit: \
`docker commit --change='CMD ["python", "-c", "import this"]' <container_id> <new-image-name>`

***

## Port Mapping
Your container needs to serve something on a port? Express server on 3000? React App on 8080?
```Dockerfile
FROM node:14

# create and cd into a working directory
WORKDIR /server

# handle installations like this first to get caching benefits if re-building
COPY ./package*.json ./
RUN npm install

COPY . . 

# expose any ports
EXPOSE 3000

CMD ["node", "server.js"]
```
`docker build . -t cool-app`

You might want to run this in detached mode (`-d`) and have it map the exposed port to a port on localhost (`-P`): \
`docker run -d -P cool-app`

Find the port the container has mapped the exposed port to: \
`docker ps` \
Note the PORTS column that will contain something like `0.0.0.0:32768->3000/tcp`

And visit/call on:
`http://localhost:<the-port>` \
Given the example just above, that would be port 32768

You can also specify which ports will be mapped where by extended our run command: \
`docker run -d -p 3000:8080 node-express`: map container port 3000 to local port 8080

***

## Basic Stopping & Starting
### Start a stopped Container
- `docker start <container-name-or-id>`
- `-d`: for detached mode
- `-it`: for interactive mode

### Attach to a running detached container
- `docker attach <container-name-or-id>`

### Stop a running container
- `docker stop <container-name-or-id>`

***

## Accessing your local data in a container
We have three options here: bind mounts, volumes and tmpfs. You are most likely to be primarily using volumes. 

If a container has a **bind mount** that points to a local host folder, you can access those files in your container, changes are immediately reflected and  they remain in the container even when it is stopped. Volumes find host folders with absolute paths so make sure you are pointing the `src` to the full path eg: `src="$(pwd)/myFolder` where `pwd` is the print working directory command to get that full path. 

**Volumes** are similar to bind mounts but with some key differences. They find host folders with relative paths and they create a new folder within your Docker directories on your machine. These created folders are fully managed by Docker and you can interact with them via the Docker CLI. Volumes are more flexible than bind mounts but they do require a bit more work to edit your files in your host machine applications eg. VSCode so for now you may want to stick to bind mounts to mount the files you want to keep working with in your editor. If you don't need direct GUI access to the data, volumes are perfect. Use cases might be databases you access programatically.

**Tmpfs** (temporary file system) does not persist the data. It allows access only for the life of the container. This is useful for sensitive information such as access codes.

Given this file (within an npm package with `node-fetch` installed - `npm init -y && npm install node-fetch`):
```js
const fs = require('fs');
const fetch = require('node-fetch')

fetch('https://api.github.com/users/getfutureproof/repos')
    .then(r => r.json())
    .then(writeData)

function writeData(data){
    data.forEach(repo => {
        fs.writeFileSync('fp-public-repos.txt', `${repo.name}\n`, {'flag':'a'});
    })
}
```

And this Dockerfile:
```Dockerfile
FROM node:14
COPY . /
CMD ["node", "fetchData.js"]
```

Built to an image called fetch-stuff: \
`docker build . -t fetch-stuff`

### Run with bind mount
```
docker run -d \
--name bind-fetch-write \
--mount type=bind,source="$(pwd)"/output,dst=/output \
fetch-stuff
```
Note how the /output folder in your local working directory gets an updated file.

### Run with volume
```
docker run -d \
--name volume-fetch-write \
--mount type=volume,source=output,dst=/output \
fetch-stuff
```
Note how the /output folder in your local working directory does not get an updated file.

To interact with Docker volumes:
- `docker volume ls`: to list all
- `docker volume inspect <volume-name>`: to get info such as location
- `docker volume rm <volume-name>`: to remove volume
- `docker volume prune`: to remove all unused volumes


### Run with tempfs
```
docker run -d \
--name temp-fetch-write \
--mount type=tempfs,dst=/output \
fetch-stuff
```
Note that the resulting /output folder will disappear as soon the container stops running.

***

## Version Control
We can use a form of version control with our Docker images using tags.

When building an image, the `-t` flag can take arguments of various formats
- `-t my-image`: builds an image called `my-image` with the tag of `latest`
- `-t my-image:v2`: builds an image called `my-image` with the tag of `v2`

Say you have an image tagged 'latest' and you want to give it a new tag of `v2`
- `docker tag my-image:latest my-image:v2`

Run `docker images` and note the repeat image IDs.

Note that if you don't give a tag when running an image, it will assume you mean 'latest'. So when making a new working version, you may want to retag it as `latest` when you are happy with it.

***

## Pushing an image to DockerHub
Make sure you are logged in on the CLI: \
`docker login`

Prepare your image \
`docker tag <image-name>:<image-tag> <docker-username>/<image-name>:<image-tag>` \
eg. `docker tag my-image:latest getfutureproof/my-image:latest` 

Push to DockerHub \
`docker push <docker-username>/<image-name>` \
eg. `docker push getfutureproof/my-image`

Check it out on your DockerHub profile! Others can now pull your image with `docker pull <your-username>/<your-image-name>`

***
