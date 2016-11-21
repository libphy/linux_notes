# Docker basics

## Overview
For installation, follow installation guide in docker official website. [e.g., shortcut to Ubuntu version](https://docs.docker.com/engine/installation/linux/ubuntulinux/).    
OR, follow the ["get started" document](https://docs.docker.com/engine/getstarted/step_one/) for installation & basic tutorial. The following section (**Using Docker**) is a reconstructed tutorial inspired by their 'get started' doc and someone's github repo ['Docker cheat sheet'](https://github.com/wsargent/docker-cheat-sheet).    

## Using Docker
By default, docker uses a root user. So if logged in as a non-root user (probably in most cases), 'sudo' has to be typed before the docker command. There is a way to add my user name into docker group so that I don't have to type 'sudo' every time. I didn't do it correctly, so I need 'sudo'- but I'll omit 'sudo' in the example codes below.    

### Running docker
When starting docker for the first time, by pulling/running a docker image, there is an automatic download of a docker image. And a container is created to run that image. One can remove the container (I will explain later), but the image still stays locally unless removed.   

Let's use an example of using docker hub. Docker hub ([https://hub.docker.com](https://hub.docker.com)) is a docker repository- a place to store & share public/private docker images- probably managed by the Docker company. There are other docker image repositories. For example, [**TensorFlow**](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tools/docker) provides docker images  via [google cloud's private docker repo](https://cloud.google.com/container-registry/).

#### Example 1.

Back to Docker hub example, search 'whalesay' on the search field, then there area bunch of 'whalesay' games written by different users. Select docker/whalesay, then run the image.
```bash
docker run docker/whalesay cowsay boo
```
`docker run` runs the image `docker/whalesay`, which `docker` here is the username (or the repo name) in the docker hub repos, and `whalesay` is the game (or service) name. Then `cowsay` and `boo` are command and parameters (in this example just a text) specified by the `whalesay` game. This image just shows some text-image of a whale with a specified text balloon.
Like hello-world example `docker run hello-world`, this stops after it runs.

#### Example 2.
Let's take another example- with an interactive component.
```bash
docker run -it ubuntu bash
```
This runs a bash on ubuntu. The option `-it` is actually a combination of two options `-i` and `-t` for "interactive" and "tty", which are options for the command `run` ([more options for run](https://docs.docker.com/engine/reference/run/)). To quit the bash, simply exit by typing `exit`. Exiting bash will stop the container.  

#### Monitoring docker containers
One can see status of all containers (running or stopped)
```bash
docker ps -a
```
Here, `-a` is an option to show all containers. Without it, only active containers will show up.
The result looks like this (without lines on the table):    


|CONTAINER ID | IMAGE |   COMMAND  |    CREATED   |   STATUS  |  PORTS |  NAMES  |    
|:-------:|:-----:|:-------:|:------:|:-----:|:----:|:-----:|
|d54958039959  | docker/whalesay | "cowsay 'boo! I'm a w" | 23 minutes ago | Exited (0) 23 minutes ago |    | silly_booth |
|5d0f815f52d3   |     docker/whalesay |    "cowsay boo"|             39 minutes ago  |    Exited (0) 39 minutes ago |             |         berserk_joliot|
|6704cd660274    |    ubuntu   |           "bash"|                   2 hours ago   |      Exited (0) 2 hours ago  |        |               admiring_babbage |
|58675135dfc9     |   hello-world    |     "/hello" |                2 hours ago    |     Exited (0) 2 hours ago |     |       stoic_meitner|

Note that `docker run` always creates a new container. In the example above, I have two containers from the same image `docker/whalesay`. This is because I typed `docker run docker/whalesay`.

#### Restarting a stopped container
A simple STDOUT service like `docker/whalesay` stops immediately after it runs. Quite many people asked if they can re-run the container (with changed text) without creating a new container every time they type the new texts. This is possible not by re-running existing (cut stopped) container, but by creating another container that has interactive session. To create an interactive session to a service like `docker/whalesay`, one needs to know the image id first.
Image ID can be found in the list of images.   
```
docker images
```
It seems that I have other images previously installed.  

|REPOSITORY  | TAG | IMAGE ID | CREATED | SIZE|
|:----------:|:---:|:--------:|:-------:|:---:|
|ubuntu | latest | e4415b714b62 | 4 days ago |128.1 MB|
|gcr.io/tensorflow/udacity-assignments |  0.6.0              |e591289d3de4 | 4 weeks ago |  1.032 GB|
|hello-world| latest | c54a2cc56cbb| 4 months ago|1.848 kB|
|scrapinghub/splash | latest | ee93eedb8e7f | 7 months ago|        560.7 MB|
|docker/whalesay | < none > | 6b362a9f73eb | 18 months ago | 247 MB |
In this example, the image ID for `docker/whalesay` service is 6b362a9f73eb.
```
docker run -ti --entrypoint=sh <imageID>
```
Replacing <imageID> below to 6b362a9f73eb will start an interactive session starting with `#` prompt. Type the whalesay command `cowsay`.
```
# cowsay "Hi"
```
Since it's interactive, it shows the result on the fly. One can repeat this as many times as one wishes. To exit, just type `exit`.
Checking containers list `docker ps -a` will show the new container (stopped when exit).
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS               NAMES
e8ec0125045d        6b362a9f73eb        "sh"                     17 minutes ago      Exited (0) 17 minutes ago                          silly_bardeen

```
In the example above, I assumed I already have a docker/whales image from previous docker run. However, if it was the first time of using docker/whalesay, instead of the <imageID> , one can use the image name `docker/whalesay`- of course this will pull the image from the source. By the way, when using <imageNAME> instead if <imageID> even though there is existing image, it will still run the same, but also it will try to pull the image from source (it can update). Now, once this interactive container is created I can start it again and display different texts. Just start it again using `docker start`.
```
docker start -i <containerID>
```
This interactive feature is possible because we created a container that has interactive feature by adding `--entrypoint=sh` option.    
Back to ubuntu bash example, I can start the same container in the same way (`docker start -i ...`). In bash, I created a folder and exit the container, then started it again. The folder is there.     
There is something else- `docker commit <containerID> <Repo:Tag>` to commit settings and file changes to a new image. It creates a new image with those settings.

#### Removing containers and images
One can remove a container or multiple containers.
```
docker rm <containerID> [additional containerIDs]
```
`[...]` means optional inputs.
Also, images can be removed. Use `-f` as an option to force remove.
```
docker rmi [options] <imageID> [additional containerIDs]
```
