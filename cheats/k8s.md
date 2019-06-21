# k8s

```bash
# deploy to k8s
kubectl run kubia --image=luksa/kubia --port=8080 --generator=run/v1

# delete resources
kubectl delete all
kubectl delete --all
kubectl delete pod --all
kubectl delete rc --all

# get resources with verbose logging
kubectl get pods -v=10
ks get all

# expose kubia externally
kubectl expose rc kubia --type=LoadBalancer --name kubia-http
kubectl inspect service/kubia-http
kubectl explain service/kubia-http
kubectl get svc
kubectl describe service/kubia-http
kubectl delete service/kubia-http
kubectl get

# scale a replicaset
kubectl scale rc kubia --replicas=3  
kubectl scale rc kubia --replicas=1


kubectl get pods -o wide  
kubectl describe pod kubia-6xzh2

# show k8s dashboard with minikube
minikube dashboard 
kubectl get po -o yaml  
kubectl explain pods
kubectl explain pod.spec
kubectl delete replicationcontroller/kubia
kubectl delete service/kubernetes
kubectl get all

# create resources from a file
kubectl create -f kubia-manual.yaml
kubectl get po

# get yaml for pod
kubectl get po kubia-manual -o yaml

# get logs for kubia container in kubia-manual pod
kubectl logs kubia-manual -c kubia  

# forward from port 8080 in kubia-manual to port 8888 locally
kubectl port-forward kubia-manual 8888:8080

# get logs of kubia-manual pod
kubectl logs kubia-manual
kubectl create -f kubia-manual-with-labels.yaml
kubectl get pods
kubectl get po --show-kind

# show labels for pods
kubectl get po --show-labels

# add new label to a pod
kubectl label po kubia-manual creation_method=manual 

# overrite an exisiting label
kubectl label po kubia-manual-v2 env=debug --overwrite

# show creation_method and env as columns
kubectl get po -L creation_method,env

# get pods in kube-system namespace
kubectl get po --namespace kube-system
```
