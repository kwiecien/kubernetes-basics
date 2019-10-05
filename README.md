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
