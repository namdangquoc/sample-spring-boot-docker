# sample-spring-boot-docker
Demo project for spring boot with docker

## First, install docker, maven,

## Containerize It
Docker has a simple "Dockerfile" file format that it uses to specify the "layers" of an image. So let’s go ahead and create a Dockerfile in our Spring Boot project:
```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG DEPENDENCY=target/dependency
COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","com.namdq.example.springboot.docker.SpringbootDockerApplication"]
```
replace ```com.namdq.example.springboot.docker.SpringbootDockerApplication``` to ```{package}.{Application name}```

## Build a Docker Image with Maven
In the Maven pom.xml you should add a new plugin like this (see the plugin documentation for more options): :

```
<properties>
   <docker.image.prefix>springio</docker.image.prefix>
</properties>
<build>
    <plugins>
        <plugin>
            <groupId>com.spotify</groupId>
            <artifactId>dockerfile-maven-plugin</artifactId>
            <version>1.4.9</version>
            <configuration>
                <repository>${docker.image.prefix}/${project.artifactId}</repository>
            </configuration>
        </plugin>
    </plugins>
</build>
```

To ensure the jar is unpacked before the docker image is created we add some configuration for the dependency plugin:

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>unpack</id>
            <phase>package</phase>
            <goals>
                <goal>unpack</goal>
            </goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>${project.groupId}</groupId>
                        <artifactId>${project.artifactId}</artifactId>
                        <version>${project.version}</version>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Build a Docker image
You can build a tagged docker image using the command line like this:

```
mvn install dockerfile:build
```

## Run a docker image

```
docker run -p 8080:8080 -t namdq/springboot-docker
```
