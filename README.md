<p align="center"><img width="150" alt="portfolio_view" src="logo.png"></p>
<!-- <h4 align="center"><a href="HYPERLINK HERE">URL HERE</a></h3> -->
<h1 align="center">Certified Kubernetes Administrator Notes</h1>




# Exam

- Kubernetes 1.23
- Ubuntu 20.04
-


# Basics

- Kubernetes (k8s)
    - Definition: Portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation.
    - Container orchestration
    - Application reliability (self-healing)
    - Automation

# Architecture

## Control Plane

- Manages the cluster
- Components
    - kube-api-server
       - Serves Kubernetes API
       - Primary interface to control plane and cluster itself
    - etcd
        - Backend data store for Kubernetes cluster
    - kube-scheduler
        - Selects an available node in the cluster on which to run containers
    - kube-controller-manager
        - Runs collection of multiple controller utilities in a single process
    - cloud-controller-manager
        - Interface between Kubernetes and various cloud platforms
        - Only used when using with cloud-based resources (ie. GCP, AWS, Azure)

## Nodes

- Machines where the containers managed by the cluster are run
- Components
    - kubelet
        - Kubernetes agent that runs on a node
        - Communicates with control plane
        - Handles reporting container status and other data to control plane
    - Container runtime
        - Not build into Kubernetes. Separate software.
        - Responsible for running containers on machine
        - Kubernetes supports multiple, i.e. Docker, containerd
    - kube-proxy
        - Network proxy that runs on each node
        - Provides network between containers and cluster


## Kubernetes CLI tools

- `kubeadm`
    - Tool that will simply the process of setting up our Kubernetes cluster
    - Can be used to set up muli-node Kubernetes cluster
- `kubectl`
    - Controls the Kubernetes cluster
    - Communicates with the control plane
- `kubelet` 
    - Primary node agent that runs on each Kubernetes node
    - Can register node with the API server
    - Applies, creates, updates, and destroys containers on a node
- More ...


# Installation

## `containerd` Installation (if needed)

Perform these steps on both control and worker nodes

- Load needed kernel modules (ensures when system starts up, modules will be enables)
    - `cat << EOF | sudo tee /etc/modules-load.d/containerd.conf overlay br-netfilter EOF`
- Enable the kernel modules right now
    - `sudo modprobe overlay`
    - `sudo modprobe br-netfilter`
- System level networking settings
    - ```
        cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
            net.bridge.bridge-nf-call-iptables = 1
            net.ipv4.ip_forward = 1
            net.bridge.bridge-nf-call-ip6tables = 1
            EOF
      ```

- Command to make networking settings take effect immediately
   - `sudo sysctl --system`

- Install **containerd**
    - `sudo apt-get update && sudo apt-get install -y containerd`

- Create containerd configuration directory
    - `sudo mkdir -p /etc/containerd`

- Set default configurations
    - `sudo containerd config default | sudo tee /etc/containerd/config.toml`

- Restart containerd service
    - `sudo systemctl restart containerd`


## Kubernetes Installation

Perform these steps on both control and worker nodes.

- Disable system swap memory
    - `sudo swapoff -a`

- Check system fstab file for any entries that will turn swap back on
    - `sudo cat /etc/fstab`

- Install dependencies
    - `sudo apt-get update && sudo apt-get install -y apt-transport-https curl`

- Add GPG key for the Kubernetes repository
    - `curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`

- Setup the Kubernetes repository entry
    <!-- cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list -->
    <!-- deb https://apt.kubernetes.io/ kubernetes-xenial main -->
    <!-- EOF -->

- Get newly added repository information
    - `sudo apt-get update`

- Install Kubernetes packages (note version)
    - `sudo apt-get install -y kubelet=1.23.0-00 kubeadm=1.23.0-00 kubectl=1.23.0-00`

- Prevent automatic updating of Kubernetes packages for more control
    - `sudo apt-mark hold kubelet kubeadm kubectl`






### Useful Commands

Setting Custom Hostname: `sudo hostnamectl set-hostname k8s-control`
Set up host file: `sudo vim /etc/hosts`
    - Add private IPs for all servers
    - `<PRIVATE IP> <HOSTNAME>`








