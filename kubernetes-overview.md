# Kubernetes (K8S)

Kubectl - CLI tool for Kubernetes

Master Node:
- The main machine that controls the nodes
- Main entrypoint for all administrative tasks
- It handles the orchestration of the worker nodes

Worker Node:
- It is a worker machine in Kubernetes (used to be known as minion)
- This machine performs the requested tasks. Each Node is controlled by the Master Node
- Runs containers inside pods
- This is where the Docker engine runs and takes care of downloading images and starting containers

Kubelet:
- Primary node agent
- Ensures that containers are running and healthy

_Kubernetes does not schedule containers directly, but Pods which describes how to run one or multiple containers simultaneously._

Kubernetes Pod:
- A Pod can host multiple containers and storage volumes
- Pods are instances of Deployments (see Deployment)
- One Deployment can have multiple pods
- With Horizontal Pod Autoscaling, Pods of a Deployment can be automatically started and halted based on CPU usage
- Containers within the same pod have access to shared volumes
- Each Pod has its unique IP Address within the cluster
- Pods are up and running until someone (or a controller) destroys them
- Any data saved inside the Pod will disappear without a persistent storage

Deployment:
- A deployment is a blueprint for the Pods to be create (see Pod)
- Handles update of its respective Pods.
- A deployment will create a Pod by it’s spec from the template.
- Their target is to keep the Pods running and update them (with rolling-update) in a more controlled way.
- Pod(s) resource usage can be specified in the deployment.
- Deployment can scale up replicas of Pods.

Secret:
- A Secret is an object, where we can store sensitive informations like usernames and passwords.
- In the secret files, values are base64 encoded.
- To use a secret, we need to refer to the secret in our Pod.
- Or we can put it inside a volume and mount that to the container.
- Secrets are not encrypted by default. For encryption we need to create an EncryptionConfig.

Service:
- A service is responsible for making our Pods discoverable inside the network or exposing them to the internet
- A Service identifies Pods by its LabelSelector
- 3 types of service:
  - ClusterIP:
    - The deployment is only visible inside the cluster
    - The deployment gets an internal ClusterIP assigned to it
    - Traffic is load balanced between the Pods of the deployment
  - Node Port:
    - The deployment is visible inside the cluster
    - The deployment is bound to a port of the Master Node
    - Each Node will proxy that port to your Service
    - The service is available at http(s)://:/
    - Traffic is load balanced between the Pods of the deployment

  - Load Balancer:
    - The deployment gets a Public IP address assigned
    - The service is available at http(s)://:<80||42>/
    - Traffic is load balanced between the Pods of the deployment




## Notes
- nodes mean machines
