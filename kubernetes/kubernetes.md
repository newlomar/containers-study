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
