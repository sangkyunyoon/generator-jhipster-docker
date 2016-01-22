# generator-jhipster-docker
[![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Dependency Status][daviddm-image]][daviddm-url]
> JHipster module, Docker support in your JHipster application

<div align="center">
  <a href="http://jhipster.github.io">
    <img src="https://github.com/pascalgrimaud/generator-jhipster-docker/raw/master/images/logo-jhipster.png">
  </a>
  <a href="https://www.docker.com/">
    <img width=200px src="https://github.com/pascalgrimaud/generator-jhipster-docker/raw/master/images/logo-docker.png">
  </a>
</div>

# Introduction

This is a [JHipster](http://jhipster.github.io/) module, that is meant to be used in a JHipster application. This module is used to :

- Generate docker-compose services
- Generate files for Automated build at [Docker Hub](https://hub.docker.com/)
- Containerize your application and push image to [Docker Hub](https://hub.docker.com/)


# Table of contents

  * [Prerequisites](#prerequisites)
  * [Installation](#installation)
  * [Usage](#usage)
    * [1 - Generate docker-compose services](#1---generate-docker-compose-services)
      * [1.1 - Description](#11---description)
      * [1.2 - Working with databases](#12---working-with-databases)
        * [1.2.1 - Starting MySQL, PostgreSQL or MongoDB](#121---starting-mysql-postgresql-or-mongodb)
        * [1.2.2 - Starting Cassandra the first time](#122---starting-cassandra-the-first-time)
        * [1.2.3 - Starting Cassandra the next times](#123---starting-cassandra-the-next-times)
      * [1.3 - Working with Sonar](#13---working-with-sonar)
      * [1.4 - Common commands](#14---common-commands)
        * [1.4.1 - List the containers](#141---list-the-containers)
        * [1.4.2 - Stop the containers](#142---stop-the-containers)
        * [1.4.3 - Delete a container](#143---delete-a-container)
    * [2 - Automated build at the Docker Hub](#2---automated-build-at-the-docker-hub)
      * [2.1 - Description](#21---description)
      * [2.2 - Generate the files](#22---generate-the-files)
      * [2.3 - Set your Docker Hub project](#23---set-your-docker-hub-project)
    * [3 - Containerize your application and push image to Docker Hub](#3---containerize-your-application-and-push-image-to-docker-hub)
      * [3.1 - Description](#31---description)
      * [3.2 - Containerize your application](#32---containerize-your-application)
      * [3.3 - Examples](#33---examples)
        * [3.3.1 - Generate a JHipster application](#331---generate-a-jhipster-application)
        * [3.3.2 - Containerize your JHipster application](#332---containerize-your-jhipster-application)
        * [3.3.3 - Use src/main/docker/app.yml](#333---use-srcmaindockerappyml)
        * [3.3.4 - Add spring properties](#334---add-spring-properties)
        * [3.3.5 - Start in dev profile](#335---start-in-dev-profile)
  * [License](#license)



# Prerequisites

As this is a [JHipster](http://jhipster.github.io/) module, we expect you have JHipster and its related tools already installed:

- [Installing JHipster](https://jhipster.github.io/installation.html)

You have to install Docker and Docker Compose:

- [Docker](https://docs.docker.com/installation/#installation)
- [Docker Compose](https://docs.docker.com/compose/install)

To use *Automated build* or *Pushing your application to Docker Hub*, you have to create an account at:

- [https://hub.docker.com/](https://hub.docker.com/)

All these images come from the official [Docker Hub](https://hub.docker.com/):

- [MySQL](https://hub.docker.com/_/mysql/)
- [PostgreSQL](https://hub.docker.com/_/postgres/)
- [MongoDB](https://hub.docker.com/_/mongo/)
- [Cassandra](https://hub.docker.com/_/cassandra/)
- [Elasticsearch](https://hub.docker.com/_/elasticsearch/)
- [SonarQube](https://hub.docker.com/_/sonarqube/)
- [Java](https://hub.docker.com/_/java/)
- [Tomcat](https://hub.docker.com/_/tomcat/)
- [JBoss Wildfly](https://hub.docker.com/r/jboss/wildfly/)

# Installation

To install this module:

```bash
npm install -g generator-jhipster-docker
```

To update this module:
```bash
npm update -g generator-jhipster-docker
```

# Usage

To run the module on a JHipster generated application:

```bash
yo jhipster-docker
```

You can use this command to generate docker-compose services with default options:

```bash
yo jhipster-docker default
```

To force the generator:

```bash
yo jhipster-docker default --force
```


## 1 - Generate docker-compose services

### 1.1 - Description

When using the option *Generate docker-compose services*, if your project uses MySQL, PostgreSQL, MongoDB or Cassandra, these files will be generated in your folder project:

- src/main/docker/dev.yml
- src/main/docker/prod.yml
- src/main/docker/sonar.yml

If your project uses Elasticsearch as search engine, the configuration will be included in the `src/main/docker/prod.yml` file.

So you can use docker-compose to start your database in development or production profile.

The main JHipster Generator already generated the docker-compose services described here: [docker_compose](https://http://jhipster.github.io/docker_compose.html). You can use this option to use a specific version of database or to use volumes.

### 1.2 - Working with databases

#### 1.2.1 - Starting MySQL, PostgreSQL or MongoDB

**In development profile**:

```bash
docker-compose -f src/main/docker/dev.yml up -d
```

**In production profile** (it will start ElasticSearch too if you selected it as search engine):

```bash
docker-compose -f src/main/docker/prod.yml up -d
```

#### 1.2.2 - Starting Cassandra the first time

**In development profile**:

Build the image, which will contain the CQL scripts generated by your project for initializing the database:

```bash
docker-compose -f src/main/docker/dev.yml build
```

Start the container (it will show the container id):

```bash
docker-compose -f src/main/docker/dev.yml up -d
```

Copy the cql scripts inside the container:

```bash
docker cp src/main/resources/config/cql/ "container id":/
```

Initialize the database by creating the Keyspace and the Tables:

```bash
docker exec -it "container id" init
```

- After using entity sub generator, update the cql scripts inside the container:

```bash
docker cp src/main/resources/config/cql/ "container id":/
```

- Create the tables:

```bash
docker exec -it "container id" entities
```

**In production profile** (with Clusters):

Build the image:

```bash
docker-compose -f src/main/docker/prod.yml build
```

Start the container (it will show the container id):

```bash
docker-compose -f src/main/docker/prod.yml up -d
```

Copy the cql scripts inside the container:

```bash
docker cp src/main/resources/config/cql/ "container id":/
```

Initialize the database by creating the Keyspace and the Tables:

```bash
docker exec -it "container id" init
```

Add X other nodes (it's optional):

```bash
docker-compose -f src/main/docker/prod.yml scale <name_of_your_app>-cassandra-node=X
```

Manage nodes with OpsCenter: [http://localhost:8888](http://localhost:8888)

Before starting your application in production profile, add in your `application-prod.yml` every IP of containers to the key `spring.data.cassandra.contactPoints`

After using entity sub generator, update the cql scripts inside the container:

```bash
docker cp src/main/resources/config/cql/ "container id":/
```

Create the tables:

```bash
docker exec -it "container id" entities
```

#### 1.2.3 - Starting Cassandra the next times

**In development profile**:

```bash
docker-compose -f src/main/docker/dev.yml up -d
```

**In production profile**:

```bash
docker-compose -f src/main/docker/prod.yml up -d
```

### 1.3 - Working with Sonar

When generating your application, the `src/main/docker/sonar.yml` is generated in your folder project.
So you can start a sonar instance to analyze your code:

Start a sonar instance:

```bash
docker-compose -f src/main/docker/sonar.yml up -d
```

Analyze your code with Maven:

```bash
mvn sonar:sonar
```

Analyze your code with Gradle:

```bash
./gradlew sonar
```

You can access to sonar: [http://localhost:9000](http://localhost:9000)

### 1.4 - Common commands

#### 1.4.1 - List the containers

You can use `docker ps -a` to list all the containers

    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
    fc35e1090021        mysql               "/entrypoint.sh mysql"   4 seconds ago       Up 4 seconds        0.0.0.0:3306->3306/tcp   sampleapplication-mysql


#### 1.4.2 - Stop the containers

**In development profile**:

```bash
docker-compose -f src/main/docker/dev.yml stop
```

**In production profile**:

```bash
docker-compose -f src/main/docker/prod.yml stop
```

You can use directly docker:

```bash
docker stop "container id"
```

When you stop a container, the data are not deleted, unless you delete the container.

#### 1.4.3 - Delete a container

:warning: **Warning!** All data will be deleted (unless you used volumes):

```bash
docker rm "container id"
```

## 2 - Automated build at the [Docker Hub](https://hub.docker.com)

### 2.1 - Description

When using the option *Generate files for Automated build*, [Docker Hub](https://hub.docker.com) will build a Docker image everytime you commit to your repository.

### 2.2 - Generate the files

- Launch : `yo jhipster-docker`
- Select the option : `Generate files for Automated build at https://hub.docker.com/`
- Answer all questions
    - Select the version of your database
    - Select the version of ElasticSearch
    - Put the URL of your Git repository
    - Put your Docker Hub username

### 2.3 - Set your Docker Hub project

- Go to [https://hub.docker.com/r/username/](https://hub.docker.com/r/username/) (replace username by yours)
- Menu Create:
    - Select Create Automated Build
    - Select the repository
    - Put a description, then click on create
<div align="center">
  <a href="http://jhipster.github.io">
    <img src="https://github.com/pascalgrimaud/generator-jhipster-docker/raw/master/images/automated-step1.png">
  </a>
  <br/>
  <a href="https://www.docker.com/">
    <img src="https://github.com/pascalgrimaud/generator-jhipster-docker/raw/master/images/automated-step2.png">
  </a>
</div>


- Go to Build Settings
    - Choose your branch or let master by default
    - Put this Dockerfile location: src/main/docker/hub/
    - Click on Save Changes
<div align="center">
  <a href="https://www.docker.com/">
    <img src="https://github.com/pascalgrimaud/generator-jhipster-docker/raw/master/images/automated-step3.png">
  </a>
</div>


- Return to this project: git commit and push these changes!
- Go to Build details: it should be a new line with Building
- Go to Repo info and copy/paste in Full description the `src/main/docker/app-hub.yml`

## 3 - Containerize your application and push image to [Docker Hub](https://hub.docker.com/)

### 3.1 - Description

This option is used to build a Docker image for running the JHipster application.
You can read this documentation for more details:
[spring-boot-docker](https://spring.io/guides/gs/spring-boot-docker/)

If you want, you can push your Docker image to Docker Hub.

:warning: **Warning!** Don't put your credentials in config files, if you decided to push your application to [Docker Hub](https://hub.docker.com/)

### 3.2 - Containerize your application

- Launch : `yo jhipster-docker`
- Select the option : `Containerize your application and push image to https://hub.docker.com/`
- Answer all questions
    - Select the base image
    - If you selected Tomcat or WildFly, put the url
    - Select the version of your database
    - Select the version of ElasticSearch
    - Use volume or not
    - Put your Docker Hub username
    - Put your the tag
    - Choose if you want to push your image to [Docker Hub](https://hub.docker.com/)

:hourglass_flowing_sand: **Be patient!** This may take several minutes, depending on the speed of your connection.

### 3.3 - Examples

#### 3.3.1 - Generate a JHipster application

Use this `.yo-rc.json` to generate your JHipster application. All options are default, except ElasticSearch :

```yaml
{
  "generator-jhipster": {
    "baseName": "jhipster",
    "packageName": "com.mycompany.myapp",
    "packageFolder": "com/mycompany/myapp",
    "authenticationType": "session",
    "hibernateCache": "ehcache",
    "clusteredHttpSession": "no",
    "websocket": "no",
    "databaseType": "sql",
    "devDatabaseType": "h2Disk",
    "prodDatabaseType": "mysql",
    "searchEngine": "elasticsearch",
    "useSass": false,
    "buildTool": "maven",
    "frontendBuilder": "grunt",
    "enableTranslation": true,
    "enableSocialSignIn": false,
    "rememberMeKey": "24d8cfa8a79d120b76c5b6076a766fa4eaee525f",
    "testFrameworks": [
      "gatling"
    ]
  }
}
```

#### 3.3.2 - Containerize your JHipster application

- Launch : `yo jhipster-docker`
- Select the option : `Containerize your application and push image to https://hub.docker.com/`
- Answer all questions
    - Select the base image : use default (Java)
    - Select the version of your database : use default
    - Select the version of ElasticSearch : use default
    - Use volume or not : use default (No)
    - Put your Docker Hub username : put `example`
    - Put your the tag : use default (latest)
    - Choose if you want to push your image to [Docker Hub](https://hub.docker.com/) : use default (No)

#### 3.3.3 - Use src/main/docker/app.yml

The `src/main/docker/app.yml` is generated:

```yaml
jhipster-app-elasticsearch:
  container_name: jhipster-app-elasticsearch
  image: elasticsearch:1.7.3
  ports:
    - "9200:9200"
    - "9300:9300"
jhipster-app-mysql:
  container_name: jhipster-app-mysql
  image: mysql:5.7.9
  environment:
    - MYSQL_USER=root
    - MYSQL_ALLOW_EMPTY_PASSWORD=yes
    - MYSQL_DATABASE=jhipster
  ports:
    - "3306:3306"
  command: mysqld --lower_case_table_names=1
jhipster-app:
  container_name: jhipster-app
  image: example/jhipster:latest
  ports:
    - "8080:8080"
  links:
    - "jhipster-app-elasticsearch:elastic"
    - "jhipster-app-mysql:mysql"
```

You can use this file to start ElasticSearch, MySQL. Then, your JHipster application will start after 20sec:
```bash
docker-compose -f src/main/docker/app.yml up
```

#### 3.3.4 - Add spring properties

You can add other spring-boot properties, using this variable environment **JHIPSTER_SPRING_ADD**. For example, to redefine the server.port:

```yaml
jhipster-app-elasticsearch:
  container_name: jhipster-app-elasticsearch
  image: elasticsearch:1.7.3
  ports:
    - "9200:9200"
    - "9300:9300"
jhipster-app-mysql:
  container_name: jhipster-app-mysql
  image: mysql:5.7.9
  environment:
    - MYSQL_USER=root
    - MYSQL_ALLOW_EMPTY_PASSWORD=yes
    - MYSQL_DATABASE=jhipster
  ports:
    - "3306:3306"
  command: mysqld --lower_case_table_names=1
jhipster-app:
  container_name: jhipster-app
  image: example/jhipster:latest
  environment:
    - JHIPSTER_SPRING_ADD=--server.port=18080
  ports:
    - "8080:18080"
  links:
    - "jhipster-app-elasticsearch:elastic"
    - "jhipster-app-mysql:mysql"
```

#### 3.3.5 - Start in dev profile

You can use this variable environment **JHIPSTER_SPRING** to redefine all spring-boot properties when starting the JHipster application. For example, you can start this application in dev profile, without ElasticSearch and MySQL. The application will use H2 database:

```yaml
jhipster-app:
  container_name: jhipster-app
  image: example/jhipster:latest
  environment:
    - JHIPSTER_SLEEP=1
    - JHIPSTER_SPRING=--spring.profiles.active=dev
  ports:
    - "8080:8080"
```

# License

Apache-2.0 © [Pascal Grimaud](https://twitter.com/pascalgrimaud)

[npm-image]: https://img.shields.io/npm/v/generator-jhipster-docker.svg
[npm-url]: https://npmjs.org/package/generator-jhipster-docker
[travis-image]: https://travis-ci.org/pascalgrimaud/generator-jhipster-docker.svg?branch=master
[travis-url]: https://travis-ci.org/pascalgrimaud/generator-jhipster-docker
[daviddm-image]: https://david-dm.org/pascalgrimaud/generator-jhipster-docker.svg?theme=shields.io
[daviddm-url]: https://david-dm.org/pascalgrimaud/generator-jhipster-docker
