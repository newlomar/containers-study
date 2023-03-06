# Continuing from where I stopped studying at work - 35:04

## Minicube and Kubectl

- What is Minicube

- What is kubectl

- Set-up Minicube cluster

### What is Minicube?

Before talking about minicubes, normally in a **Production Cluster Setup** of Kubernetes you will have at least 2 master nodes and multiple worker nodes. Master node and worker node have separated responsability and they normally stays in separated virtual or physical machines. If you wanna test something in your local enviroment, or try something different very quickly, like deploying new application or compoenents and you wanna test on your local machine before sending to production, setting up a cluster like the production cluster will be pretty difficult or impossible. So, for this problem there is a open source tool that is called **Minicube**. Minicube is a one node cluster where the master processes and the worker processes both run on one node. This node will have docker runtime pre-installed so you be able to run the containers or pods with the containers on this node. And the way that minicube will run the node on local machine will be through a virtual box or some other hypervisor. Minicube is **a One kubernetes cluster that runs in a virtualbox**.

Summary in points

- Minicube creates a Virtual Box on your computer
- The Node runs in that Virtual Box
- It has only 1 Node K8s on the cluster
- It is used for testing purposes

### What is Kubectl

Now that you have a minicube, you need a way to interact with the cluster. The way to do it is with **kubectl**. The way to interact with a Kubernete cluster is talking to the API Server. You can do that by an UI, by API or CLI (Kubectl). Kubectl is the most powerful of the 3 clients.

Kubectl is not only used to talk with minicube cluster, you can use it talk with a cloud cluster, or any other type of kubernetes cluster.

### Why there is no kubectl create pod command

Pod is the smallest unit but, you are creating a deployment which is an abstrastraction and the deployment will create the pods later.

Example of command:

```sh
kubectl create deployment NAME --image=image [--dry-run] [options]
```

example for nginx image:

```sh
kubectl create deployment nginx-depl --image=nginx
```

this command basically is/have:

- blueprint for creating pods

- most basic configuration for deployment (name and image to use)

- rest defaults

### Layers of Abstraction

**Deployment** manages a _Replica Set_

**ReplicaSet** manages a _Pod_

**Pod** is an abstraction of _Container_

Everything below Deployment is handled by Kubernetes

## K8s YAML Configuration File

Each configuration file has 3 parts

1 - Metadata

2 - Specification (spec)

- Atributes of "spec" are specific to the kind!

3 - Status

- Automatically generated and added by Kubernetes!

Kubernetes will always compare what is the desired status and what is the actual status. If they do not match, it will try to fix it. This is the basis of "self-healing" feature that kubernetes provides.

Kubernetes updates the state continuously.

Where does K8s get this status data?

- From etcd

Etc holds the current status of any K8s component!

### Format of configuration file

Yaml

- "**Human friendly\*\*** data serialization standard for all programming languages"

- syntax: strict indentation!

- Yaml validator can be useful to check if the yaml file is correct

- Store the config file with your code or in a specific git repository

#### Template

Is basic a configuration file inside of a configuration file

Has its own metadata and "spec" seciton

The configuration of the templates applies to Pod

It is a blueprint for a pod

### Connecting components (Labels & Sectors & Ports)

Metadata part of the configuration file contains the **Labels**

Specification (spec) part contains the **Selectors**

#### Connecting Deployment to Pods

- Any key-value pair for component

  - example -> app: nginx

- Pods get the label through the template blueprint

- This label is matched by the Selector:
  - example:
    ```yaml
    selector:
      matchLabels:
        app: nginx
    ```
- In the _spec_ of the **Service** we define a specification which basically makes a connection between Service and deployment or its pods.
  - example:
    ```yaml
    spec:
      selector:
        app: nginx
    ```

### Ports in Service and Pods (how the connection is made)

The Service has a port where the service itself is acessible. So if other service sends a request to this specific service, it has to send it to the port that is defined on this service specification. Then, the service has to know to which pod it should forward the request but also has to know which port this pod is listening, which is the **targetPort**, which should match the **container port**

## Organizing your components with K8s namespaces

What is a namespace?

Organize resources in namespaces, which you can have multiple namespaces in a cluster

Is like a Virtual Cluster inside a cluster

4 namespaces are created by default in a cluster

About each default namespace:

kube-system

- Should not be changed by the user
- System processes
- Master and Kubectl processes

kube-public

- Publicely accessible data
- A configmap, which contains cluster information

kube-node-lease

- hearbeats of nodes
- each node has associated lease object in namespace
- determines the availability of a node

default

- resources you created are located here

### Namespaces Use Cases

You could have namespaces for:

- Database
- Monitoring
- Elastic Stack
- Nginx-Ingress

Usecases / Situations

1 - Conflicts: Many teams, same application

- Two teams use the same application name, but with different configuration, and deploy to the same cluster. In this case, the second team would override the first team project. If they use an automated deployment tool they wouldn't even know that they override other team deployment.

2 - Resource Sharing: Staging and Development enviroments

- Both enviroments can use the same resources like Nginx-Ingress Controller or Elastic Stack

3 - Resource Sharing: Blue / Green Deployment

- You have differents version of the application, that will consume the same resources.

4 - Access and Resource Limits on Namespaces

- Grant access to Teams only on their own namespaces. They can't have access to other namespaces.

- Limit CPU, RAM, Storage per NameSpace

### Some Characteristics that must be analyzed when choose to use namespaces

You can't access most resources from another Namespace

- Each NameSpace must define own Configmap
- Each NameSpace must define own Secret

You can access service in another namespace

Some components can't be created within a namespace, because they live globally in a cluster and you can't isolate them

- Volume
- Node
- How to list them:
  - kubectl api-resources --namespaced==false
  - To list the components that are bound to namespace:
    - kubectl api-resources --namespaced==true
