## Docker and Kubernetes: The Complete Guide  
https://www.udemy.com/docker-and-kubernetes-the-complete-guide/

## How to remove images with '\<none\>'?
docker image ls -a | grep '^\<none\>' | awk '{print $3}' | xargs docker rmi

## Kubernetes commands:

minikube start <br />
minikube status <br />
minikube ip <br />

kubectl cluste-info <br />
kubectl apply -f <file-name.yaml> <br />
kubectl get pods <br />
kubectl get pods -o wide <br />
kubectl get services <br />

kubectl describe <object-type> <object-name> <br />
kubectl describe pod clien-pod <br />

kubectl delete -f <config file> <br />
kubectl get deployments <br />
kubectl set image <object_type>/<object_name> <container_name>=<new image to use> <br />
kubectl set image deployment/client-deployment client=marshad1/multi-client:v5 <br />

Configure VM to use Your Docker Server: eval $(minikube docker-env) 
