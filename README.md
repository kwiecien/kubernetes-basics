# kubernetes-basics
https://kubernetes.io/docs/tutorials/kubernetes-basics/

# Learn Kubernetes Basics

## Create a Cluster

#### Cluster up and running
```sh
minikube version
minikube start
```

#### Cluster version
```sh
kubectl version
```

#### Cluster details
```sh
kubectl cluster-info
kubectl get nodes
```

## Deploy an App

#### kubectl basics
```sh
kubectl version
kubectl get nodes
```

#### Deploy our app
```sh
kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
kubectl get deployments
```

#### View our app
```sh
kubectl proxy 
// Starting to serve on 127.0.0.1:8001

export POD_NAME=$(kubectl get pods -o go-template '{{range .items}}{{.metadate.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
// Name of the Pod: kubernetes-bootcamp-5b48cfdcbd-tv7z2

curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
// Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-5b48cfdcbd-tv7z2 | v=1
```

## Explore Your App
#### Check application configuration
```sh
kubectl get pods
kubectl describe pods
```

#### Show the app in the terminal
```sh
kubectl proxy
// Starting to serve on 127.0.0.1:8001
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
// Name of the Pod: kubernetes-bootcamp-5b48cfdcbd-kmlmj
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
// Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-5b48cfdcbd-kmlmj | v=1
```

#### View the container logs
```sh
kubectl log $POD_NAME
```

#### Executing command on the container
```sh
kubectl exec $POD_NAME env
kubectl exec -ti $POD_NAME bash
# cat server.js
# curl localhost:8080
# exit
```

## Expose Your App Publicly
####
```sh
```

####
```sh
```

## 
####
```sh
```
