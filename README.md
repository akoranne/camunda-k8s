# Camunda on Kubernetes

Run Camunda BPM on Kubernetes (K8s)


## Running Locally

To run Camunda in a K8s cluster locally, here are the steps.

#### MiniKube

Minikube allows running a kubernetes cluster locally within minutes. 

Follow the insturctions to install [MiniKube](https://kubernetes.io/docs/tasks/tools/install-minikube/) for your operating system.

Start minikube

```
$ minikube version
minikube version: v0.25.0

$ minikube start
There is a newer version of minikube available (v0.25.2).  Download it here:
https://github.com/kubernetes/minikube/releases/tag/v0.25.2

To disable this notification, run the following:
minikube config set WantUpdateNotification false
Starting local Kubernetes v1.9.0 cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
Loading cached images from config file.
	
$ minikube status
minikube: Running
cluster: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100

```

#### KubeCtl

Install [Kubernetes Command Line Tool (kubectl)](https://kubernetes.io/docs/tasks/tools/install-kubectl/) on your local machine. 

Test KubeCtl

```
# --- check version
$ kubectl version
	
# --- see help
$ kubectl

# --- initial state
$ kubectl get pods,deployments,services
	
NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
svc/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   62d
	
```

#### Build & Run

##### Clone the git repo 
	
```
$ cd <workspace>
$ git clone https://github.com/akoranne/camunda-k8s
```

##### Deploy application to k8 cluster

```
# --- change dir to the cloned repo
$ cd <workspace>/camunda-k8s
	

# --- create the pods using the deployment
$ kubectl apply -f deployment.yml
deployment "k8-camunda-deployment" created

# --- get list of pods created from the deployment
$ kubectl get pods -l app=k8-camunda
NAME                                    READY     STATUS    RESTARTS   AGE
k8-camunda-deployment-bc9596c77-7ljls   1/1       Running   0          14s

	
# --- apply the service
$ kubectl apply -f service.yml

# --- get details
$ kubectl get pods,deployment,service -l app=k8-camunda
NAME                                       READY     STATUS    RESTARTS   AGE
po/k8-camunda-deployment-bc9596c77-7ljls   1/1       Running   0          51s

NAME                           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/k8-camunda-deployment   1         1         1            1           6m

NAME                     TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
svc/k8-camunda-service   NodePort   10.109.17.109   <none>        8080:30234/TCP   9s

	
# --- get url to the service
$ minikube service k8-camunda-service --url
http://192.168.99.100:30234

# --- go to the k8-camunda application
$ minikube service k8-camunda-service
```

##### Test the application

Test the Camunda BPM application 

* In browser, go to the url http://<k8-camunda-url>/camunda.index.html
* Login demo/demo.
* Use the BPM tool

##### Clean Up

```
# --- get current state
$ kubectl get pods,deployments,services,secrets -l app=k8-camunda

# --- delete service
$ kubectl delete -f service.yml

# --- delete deployment
$ kubectl delete -f deployment.yml

# --- verify 
$ kubectl get pods,deployments,services

# --- shutdown minikube
$ minikube stop
```



