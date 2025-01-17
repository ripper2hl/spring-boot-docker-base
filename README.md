# Spring Boot Docker Base Image

[![](https://img.shields.io/docker/stars/jesusperales/spring-boot.svg)](https://hub.docker.com/r/jesusperales/spring-boot/ 'Docker hub')
[![](https://img.shields.io/docker/pulls/jesusperales/spring-boot.svg)](https://hub.docker.com/r/jesusperales/spring-boot/ 'Docker hub')

## What is this?

## It's a fork of the original [spring boot base](https://github.com/EtienneK/spring-boot-docker-base) image with [Amazon Corretto](https://aws.amazon.com/es/corretto/) and Java 11

Docker base image, following Docker image
[best-practices](http://www.projectatomic.io/docs/docker-image-author-guidance/),
for Spring Boot applications.

Some features:

  - The Spring Boot application runs as a non-root user inside 
    the container
  - Injection of JVM- and application arguments at runtime
  - Wrapper script that will forward Unix signals (example: 
    `SIGINT`) to the Spring Boot application
  - Runs on RedHat OpenShift out of the box

## How do I use it?

Using this base image to create an image for your Spring
Boot application is easy:

  1. Create a new Dockerfile and use this image as the 
     `FROM` image
  1. Make sure the Spring Boot application jar (must be named `app.jar`) 
     file is in the same directory as the Dockerfile you've created above
  1. Build your Dockerfile

Here's an example on how you would use it
(Replace `/path/to/spring/boot/application.jar`
to the actual path of your Spring Boot application jar file):

```bash
mkdir bootapp-docker && cd bootapp-docker
echo "FROM jesusperales/spring-boot" > Dockerfile
cp /path/to/spring/boot/application.jar ./app.jar
docker build -t bootapp-docker .
docker run -p 8007:8007 bootapp-docker
```

Then open up your browser to
[http://localhost:8007/](http://localhost:8007/) and you
should see your Spring Boot application's index page.

## Injecting JVM- and application arguments

To inject JVM- and application arguments during runtime,
simply override the `BOOTAPP_JAVA_OPTS` (for JVM) and 
`BOOTAPP_OPTS` (for application) environment variables.
Example:

```bash
docker run -p 8007:8007 \
  -e BOOTAPP_JAVA_OPTS="-Xms512m -Xmx1024m" \
  -e BOOTAPP_OPTS="--spring.profiles.active=test --spring.main.banner-mode=off" \
  bootapp-docker
```

## Example Project

[https://github.com/EtienneK/spring-boot-admin](https://github.com/EtienneK/spring-boot-admin)

## Multi-stage builds

If you use docker mulit-stage builds you need create an `app.jar` file :

```bash
echo 'workaround for use multi-stage builds' >> app.jar 
```

And this is a Dockerfile example:

```Dockerfile
FROM maven:alpine AS build
ADD pom.xml /usr/src/app/
ADD src /usr/src/app/src
RUN mvn -f /usr/src/app/pom.xml clean package -Dmaven.test.skip=true

FROM jesusperales/spring-boot
COPY --from=build /usr/src/app/target/*.jar /
EXPOSE 8007
```
