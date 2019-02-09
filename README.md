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
* Remove the application once finished testing (e.g. localhosst:8080/hello)
```
helm delete --purge hello
```

## Deploy to AWS EKS

Now we will deploy the application to the cloud. 
Use the templates in tutorial [EKS getting started](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html) to create a VPC for our EKS application. We will need the following details to continue: 
```
Security Group: sg-xxxx
VpcId: vpc-xxxx
SubnetIds: subnet-xxx1, subnet-xxx2, subnet-xxx3
```
Create the EKS Cluster using the values above 
```
aws eks --region ap-southeast-2 create-cluster --name dev --role-arn arn:aws:iam::xxx:role/eksServiceRole --resources-vpc-config subnetIds=subnet-xxx,subnet-xxx,subnet-xxx,securityGroupIds=sg-xxx
```
Once the Cluster is active, add it the the kubeconfig
```
aws eks update-kubeconfig --name dev
```
Check the new cluster is now set as the default
```
>kubectl config get-contexts
CURRENT   NAME                                                  CLUSTER                                               AUTHINFO                                              NAMESPACE
*         arn:aws:eks:ap-southeast-2:xxx:cluster/dev   arn:aws:eks:ap-southeast-2:xxx:cluster/dev   arn:aws:eks:ap-southeast-2:xxx:cluster/dev
          docker-for-desktop
```
Create worker nodes (refer to EKS getting started) from cloud formation template

https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-01-09/amazon-eks-nodegroup.yaml

Join worker nodes to the cluster (note: update role arn with output from cloud formation creation above)
```
kubectl apply -f aws-auth-cm.yaml
```
Wait until the nodes are ready
```
kubectl get nodes
```
Install helm 
```
kubectl -n kube-system create sa tiller
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
```

Deploy
```
helm install --name hello chart
```

Find the external url
```
kubectl get svc -o wide
```



## References  
[create-your-first-helm-chart](https://docs.bitnami.com/kubernetes/how-to/create-your-first-helm-chart/)

[EKS getting started](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)