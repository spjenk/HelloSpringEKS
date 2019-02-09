### Hello 
Example project deploying a Spring Boot applications to AWS EKS 

##Set-up  
* Created application via start.spring.io (Web, DevTools) 
* Added Hello Rest Controller 
* Run locally 
```
mvn spring-boot:run
``` 

##Add Docker 
* Added __dockerfile-maven-plugin__ and __maven-dependency-plugin__ to pom file
* Created docker file
* build 
```
mvn install
```
* checked BOOT-INF AND META-INF now exsit under target/dependency/
* build docker container 
```
 docker build -t spjenk/hello .
```
* run docker container 
```
docker container run --name hello -p 8080:8080 -d spjenk/hello
curl http://localhost:8080/hello
```

