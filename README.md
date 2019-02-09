### Hello 
Example project deploying a Spring Boot applications to AWS EKS 

## Prerequisities 
* Java 8 / maven etc
* Docker 
* Kubectl 
* [helm](https://github.com/helm/helm)

## Set-up  
* Created application via start.spring.io (Web, DevTools) 
* Added Hello Rest Controller 
* Run locally 
```
mvn spring-boot:run
``` 

## Add Docker 
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

## Add Kubernetes 
We are going to run Kubernetes locally first and deploy to EKS in the next step
* Ensure Kubernetes is enabled in Docker (docker > settings > Kubernetes > Enable)
* switch kube context to desktop mode
```
kubectl config use-context docker-for-desktop
```
* Create skaffold helm chart 
```
helm create chart
```
* Updated chart YAMl files 
* installed the helm chart
```
helm install --name hello chart
```
* get the external ip address (may take a minute)
```
kubectl get svc
```
* check the pods are running (number should match replicas value in values.yaml)
```
kubectl get pods
```


## helpful links 
[create-your-first-helm-chart](https://docs.bitnami.com/kubernetes/how-to/create-your-first-helm-chart/)
