# Docker images with Node.js, OpenJDK, Scala and Sbt

This repository contains **Dockerfiles** used to build containers with [Node.js](https://nodejs.org), [OpenJDK](https://openjdk.java.net), [Scala](https://www.scala-lang.org) and [Sbt](https://www.scala-sbt.org).

> This project was partly inspired by the [scala-sbt](https://github.com/hseeberger/scala-sbt) project. The idea here is to create a base image ready with all the dev tools required for a Nodejs/Scala project.

## Installation ##

After installing [Docker](https://www.docker.com), you can directy pull an image from the [Docker Hub Registry](https://hub.docker.com/r/madkonix/noss):
```
docker pull madkonix/noss:alpine
```
Or you can build an image yourself from its Dockerfile:
```
docker build -t madkonix/noss:alpine github.com/madkonix/noss.git#:alpine
```

## Usage ##

You can run an image with the following command (note the `--rm` will get the container deleted upon exit):
```
docker run -it --rm madkonix/noss:alpine
```

## Contribution policy ##

Contributions via GitHub pull requests are gladly accepted from their original author. Along with any pull requests, please state that the contribution is your original work and that you license the work to the project under the project's open source license. Whether or not you state this explicitly, by submitting any copyrighted material via pull request, email, or other means you agree to license the material under the project's open source license and warrant that you have the legal authority to do so.

## License ##

This code is open source and licensed under the [AGPL-3.0]("https://www.gnu.org/licenses/agpl-3.0.txt").
