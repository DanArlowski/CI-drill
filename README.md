# CI/CD basic task

1. fork this repository in github
https://github.com/paulczar/spring-helloworld

2. clone the forked repo locally
 
3. build the source code locally

4. run the jar file from the target folder and curl localhost:8080
 you just built the source code locally, everything seems to be working
 
 
5. run jenkins container and nexus container on your machine
```
$ echo """                                                                                                                                                           

version: '3'
services:
 nexus:
  image: sonatype/nexus3
  container_name: nexus
  ports:
    - 8081:8081
  volumes:
    - nexus-data:/var/lib/nexus
  networks:
    - default

 jenkins:
  image: jenkins/jenkins
  container_name: jenkins
  ports:
    - 8080:8080
    - 9090:9090
  networks: 
    - default
  volumes:
    - jenkins-data:/var/jenkins_home
 
volumes:
  nexus-data: {}
  jenkins-data: {}

networks:
  default:
    external:
      name: docker_default
""" > docker-compose.yaml
$ docker-compose up -d
```

6. connect to your jenkins (find the master password for first initialisation using docker logs)
7. connect to sonatype and configure a mvn repository

8. create a new job(freestyle) and connect it to the the forked git repository (to pull the source code from there)

9. add a build step to build the package and deploy the application on the same container using the command:
`java -Dserver.port=9090 -jar  target/hello-0.0.1-SNAPSHOT.jar`
this will prevent the build from stopping as it will deploy the webserver into the jenkins container,
curl localhost:9090 to check that it works and abort the build.

10. create another job and this time choose pipeline, create a jenkinsfile which:
 build the application,  and push to sonatype (use basic shell command for the whole jenkinsfile)
 use credentials in your build (jenkins built in credentials)

11. use maven to tag the version of the release, and push a new version to nexus each build with the build number


this is a basic CI pipeline - you build, test, run integration tests (when exists), running some code vulnerability tests(like sonarqube), push to artifact repository


