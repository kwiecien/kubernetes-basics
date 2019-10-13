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
#### Create a new service
```sh
kubectl get pods
kubectl get services
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
// service/kubernetes-bootcamp exposed
kubectl get services
kubectl describe services/kubernetes-bootcamp
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
// NODE_PORT=30070
curl $(minikube ip):$NODE_PORT
```

#### Using labels
```sh
kubectl describe deployment
kubectl get pods -l run=kubernetes-bootcamp
// NAME                                   READY   STATUS    RESTARTS   AGE
// kubernetes-bootcamp-5b48cfdcbd-ttghr   1/1     Running   0          9m
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
kubectl label pod $POD_NAME app=v1
kubectl describe pods $POD_NAME
kubectl get pods -l app=v1
```

#### Deleting a service
```sh
kubectl delete service -l run=kubernetes-bootcamp
kubectl get services
curl $(minikube ip):$NODE_PORT
// curl: (7) Failed to connect to 172.17.0.57 port 30070: Connection refused
kubectl exec -ti $POD_NAME curl localhost:8080
```

## Scale Your App
#### Scaling a deployment
```sh
kubectl get deployments
kubectl scale deployments/kubernetes-bootcamp --replicas=4
kubectl get pods -o wide
kubectl describe deployments/kubernetes-bootcamp
```

#### Load Balancing
```sh
kubectl describe services/kubernetes-bootcamp
// Endpoints:                172.18.0.2:8080,172.18.0.6:8080,172.18.0.7:8080 + 1 more...
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
// NODE_PORT=32765
curl $(minikube ip):$NODE_PORT
// Running on: kubernetes-bootcamp-5b48cfdcbd-hmmxg
// Running on: kubernetes-bootcamp-5b48cfdcbd-4cjrv --> Load balancing!
```

#### Step 3: Scale Down
```sh
kubectl scale deployments/kubernetes-bootcamp --replicas=2
kubectl get deployments
// READY 2/2
kubectl get pods -o wide
```

## Update your app
#### Update the version of the app
```sh
kubectl get deployments
kubectl get pods
kubectl describe pods
// Containers:
//  kubernetes-bootcamp:
//    Container ID:   docker://6c8df1e2baa746c2c21064e35c37640001217271b454604749af567be76b2189
//    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
kubectl get pods
// Status: Running -> Terminating; ContainerCreating -> Running
```

#### Verify an update
```sh
kubectl describe services/kubernetes-bootcamp
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
// NODE_PORT=32213
curl $(minikube ip):$NODE_PORT
Running on: kubernetes-bootcamp-cfc74666-qmhpl | v=2

kubectl rollout status deployments/kubernetes-bootcamp
// deployment "kubernetes-bootcamp" successfully rolled out

kubectl describe pods
// Containers:
//  kubernetes-bootcamp:
//    Container ID:   docker://9affeb38af14cad88c88676122cfb61fae43d998c894b0c6d265639b09780687
//    Image:          jocatalin/kubernetes-bootcamp:v2
```

#### Rollback an update
```sh
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10

kubectl get deployments
// NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
// kubernetes-bootcamp   3/4     2            3           23m -> Something went wrong...

kubectl get pods
// NAME                                   READY   STATUS             RESTARTS   AGE
//kubernetes-bootcamp-547469f5dd-dvqcm   0/1     ImagePullBackOff   0          21s

kubectl describe pods
// Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v10":
// rpc error: code = Unknown desc = Error response from daemon: 
// manifest for gcr.io/google-samples/kubernetes-bootcamp:v10 not found

kubectl rollout undo deployments/kubernetes-bootcamp
// deployment.extensions/kubernetes-bootcamp rolled back

kubectl get pods
kubectl describe pods
```
