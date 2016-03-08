# Docker Images
## Table of Contents
1. [Introduction](#introduction)
2. [Pre-requisite](#pre-requisite)
3. [Building docker image](#building-docker-image)
4. [Managing the image in Docker Registry](#managing-the-image-in-docker-registry)
5. [Pulling the image from Docker Registry](#pulling-the-image-from-docker-registry)

## Introduction
This document describes how to manage docker image. 
 - Create docker image for [ng2-book-finder](https://github.com/introduction-angular2/ng2-book-finder)
 - Push the docker image to [docker hub](https://hub.docker.com/).

## Pre-requisite
1. Clone the github repository [`ng2-book-finder`](https://github.com/introduction-angular2/ng2-book-finder). 
2. Create account in `docker-hub`.

## Building docker image
Please refer [Build your own image](https://docs.docker.com/mac/step_four/) for more reference.

The below steps only provide instructions for creating and building docker images.

### Build image using docker file
The `docker build -t ng2-book-finder .` command takes the `Dockerfile` in the current directory, and builds an image called `ng2-book-finder` on your local machine.

First Docker checks to make sure it has everything it needs to build.

```
ng2-book-finder (feature/docker-image) $ docker build -t ng2-book-finder .
Sending build context to Docker daemon   214 kB
```

Then, Docker loads with the `node:4.2-slim` image, as the image is not present on the current docker-machine, it downloads the image.

```
Step 1 : FROM node:4.2-slim
4.2-slim: Pulling from library/node
Status: Downloaded newer image for node:4.2-slim
 ---> 264411d7f3f9
```

The below steps are performed for building the image.
 - Expose the port
 - set the required working directory
 - copy required files for the image.
 - Do `npm install`.
 - And then compile the typescript to javascript.

```
Step 2 : EXPOSE 3000
 ---> Running in 71914456ebf1
 ---> bff560e19942
Removing intermediate container 71914456ebf1
Step 3 : WORKDIR /dist
 ---> Running in 5405a8603861
 ---> b5f28bfb0b84
Removing intermediate container 5405a8603861
Step 4 : COPY ./package.json /dist
 ---> 1ff76e6a087e
Removing intermediate container c0af467292f6
Step 5 : COPY ./app /dist/app
 ---> c03cb65d13f7
Removing intermediate container 765411be2826
Step 6 : COPY ./assets /dist/assets
 ---> 675d1ba05841
Removing intermediate container 08bc09448d01
Step 7 : COPY ./index.html /dist
 ---> e82d5f412d57
Removing intermediate container 725a2e08b91d
Step 8 : COPY ./favicon.ico /dist
 ---> 1789b98509cd
Removing intermediate container e71d39b771e0
Step 9 : RUN npm install
 ---> Running in 31392f0e6e12
 ---> 3849cb8f39aa
Removing intermediate container 31392f0e6e12
Step 10 : RUN npm run tsc:w
 ---> Running in def4e79b2e9f
npm info it worked if it ends with ok
npm info using npm@2.14.12
npm info using node@v4.2.6
npm info pretsc:w ng2-book-finder@1.0.0
npm info tsc:w ng2-book-finder@1.0.0

> ng2-book-finder@1.0.0 tsc:w /dist
> tsc -w
Removing intermediate container def4e79b2e9f
Step 11 : CMD npm run lite
 ---> Running in 29a5657a4a25
 ---> 3e957e2c6985
Removing intermediate container 29a5657a4a25
Successfully built 3e957e2c6985
```

Type `docker images` and press RETURN.
This command, lists the images you have locally.

```
ng2-book-finder (feature/docker-image) $ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ng2-book-finder     latest              3e957e2c6985        30 seconds ago      356.1 MB
node                4.2-slim            264411d7f3f9        5 weeks ago         205.4 MB
```

## Managing the image in Docker Registry

In `docker-hub` we have created an organization called as `introductiontodocker` and within that organization we are having a repository `ng2-book-finder`.

Use `IMAGE ID` and `docker tag` to tag the `ng2-book-finder` image as `introductiontodocker/ng2-book-finder`.

`IMAGE ID` of the image, `3e957e2c6985`.

    $ docker tag 3e957e2c6985 introductiontodocker/ng2-book-finder:latest

Type the docker images command again to see your newly tagged image.

    $ docker images
    REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
    introductiontodocker/ng2-book-finder   latest              3e957e2c6985        5 minutes ago       356.1 MB
    ng2-book-finder                        latest              3e957e2c6985        5 minutes ago       356.1 MB
    node                                   4.2-slim            264411d7f3f9        5 weeks ago         205.4 MB

Use the `docker login` command to log into the Docker Hub from the command line.

The format for the login command is:

    $ docker login --username=yourhubusername --email=youremail@company.com

Type the docker push command to push your image to your new repository.

    $ docker push introductiontodocker/ng2-book-finder
    The push refers to a repository [docker.io/introductiontodocker/ng2-book-finder]
    ba0686d99b15: Pushed
    3b6a456aff23: Pushed
    c7101d1d98e7: Pushed
    02e506e6c554: Pushed
    42016bc33fe1: Pushed
    e4ea979bfc47: Pushed
    328e451909ba: Pushed
    5f70bf18a086: Pushed
    d1df0a18993e: Pushed
    175c7063beb1: Pushed
    30f9a83f20f3: Pushed
    78dbfa5b7cbc: Pushed
    latest: digest: sha256:ed0bbb6c6d8b97f445f04c70cb08a369155a81cd5631db21fba3e0f0292e6a39 size: 10829


## Pulling the image from Docker Registry

In last sections, we have created, built and pushed the image to hub. Before we pull the image, we will need to remove the original image from local machine.

```
$ docker images
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
introductiontodocker/ng2-book-finder   latest              3e957e2c6985        5 minutes ago       356.1 MB
ng2-book-finder                        latest              3e957e2c6985        5 minutes ago       356.1 MB
node                                   4.2-slim            264411d7f3f9        5 weeks ago         205.4 MB
```

Remove the doker images for `ng2-book-finder`, using `docker rmi` command.

```
docker rmi -f 3e957e2c6985
Untagged: introductiontodocker/ng2-book-finder:latest
Untagged: ng2-book-finder:latest
Deleted: sha256:3e957e2c69859d3783a3b55454c35c1dc326dfbd1c0e49e338b4c1072814823c
Deleted: sha256:7a3005902e84a325fdc42ed6298fa35e274761e9c2ff33737def310a8ff77381
Deleted: sha256:3849cb8f39aa041c4503a9f88373e9b60a102a040e9145473e8b5db50cdd0889
Deleted: sha256:6cdb396fa5cbef60d0b0a0fe12129aa838633d33ffbf2e7351e5f2c43795f183
Deleted: sha256:1789b98509cd7d5493c95f400194968b85bcfe5bc8a74e5087f5ca0b7eea57ab
Deleted: sha256:c96f5119a1a6cbbae11d9e56a8150bb002d6623eac718c2f17f801d5f4ee47f2
Deleted: sha256:e82d5f412d577330edb4406f3c8d688b0645a783e56b435fae688e4659c9880d
Deleted: sha256:8020996aeafd3befde2447e6eeeea12ba4360a7f49b1095dc8fcb43c410050a3
Deleted: sha256:675d1ba05841d2bb245f1e717beb8d6165ddbec7c8e855f557ebe20120441424
Deleted: sha256:9aef910d828d4fdd7e7c437e1ca962b7638baf589e1fb97ace6d10138d86a4cd
Deleted: sha256:c03cb65d13f7ddf3b2390ef2fd7fc6454f486e0f470210c70d1281afad93e31e
Deleted: sha256:17143c3a24a4c1bf949e8cd6470b60651e75c1cb8dd355783e7e349e6fd78fc9
Deleted: sha256:1ff76e6a087ee66c0841190c440c4a426deb37f751ac87dada48d97b6cc8c108
Deleted: sha256:3b8d2a492252d1b699bc19ed319a92bfd1b800a30432bfc45e0a858cad347702
Deleted: sha256:b5f28bfb0b84bd84d621dcb742096d755b5fbd0cfbaeb4749c769d81bc05a088
Deleted: sha256:c3ac03a4d779b1f3f5571edbed67febe7e6170e46d6ec7a5f112d7746b822d86
Deleted: sha256:bff560e199421e7fc5efb75c25f70748e47f33fda274f2997ecddb051aa91d75
```

Now only the `node:4.2-slim` image exist on the docker-machine.

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node                4.2-slim            264411d7f3f9        5 weeks ago         205.4 MB
```

Pull and load a new image from your repository using the `docker run` command.

```
$ docker run -it --rm -p 3000:3000 --name ng2-book-finder introductiontodocker/ng2-book-finder:latest
Unable to find image 'introductiontodocker/ng2-book-finder:latest' locally
latest: Pulling from introductiontodocker/ng2-book-finder
03e1855d4f31: Already exists
a3ed95caeb02: Already exists
9269ba3950bb: Already exists
a89fca349225: Already exists
000e1d971a03: Already exists
8c733d23d19c: Pull complete
db8c5136d6e1: Pull complete
1d99a2a13ee7: Pull complete
54ab65fab8bf: Pull complete
93f80ef8648b: Pull complete
7a401bd882fb: Pull complete
17816ee60956: Pull complete
Digest: sha256:ed0bbb6c6d8b97f445f04c70cb08a369155a81cd5631db21fba3e0f0292e6a39
Status: Downloaded newer image for introductiontodocker/ng2-book-finder:latest
npm info it worked if it ends with ok
npm info using npm@2.14.12
npm info using node@v4.2.6
npm info prelite ng2-book-finder@1.0.0
npm info lite ng2-book-finder@1.0.0

> ng2-book-finder@1.0.0 lite /dist
> lite-server

[BS] Access URLs:
 -----------------------------------
       Local: http://localhost:3000
    External: http://172.17.0.2:3000
 -----------------------------------
          UI: http://localhost:3001
 UI External: http://172.17.0.2:3001
 -----------------------------------
[BS] Serving files from: ./
[BS] Watching files...

```
