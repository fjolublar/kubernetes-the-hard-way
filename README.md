# Kubernetes The Hard Way

Updated: March 2024

Understanding of this lab is *not* required to pass the CKA. It is outside the scope of the exam.

This tutorial walks you through setting up Kubernetes the hard way on a local machine using a hypervisor and using the individual service components and certificates to build a controlplane.
This guide is not for people looking for a fully automated command to bring up a Kubernetes cluster.
If that's you then check out [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine), or the [Getting Started Guides](http://kubernetes.io/docs/getting-started-guides/).

Kubernetes The Hard Way is optimized for learning, which means taking the long route to ensure you understand each task required to bootstrap a Kubernetes cluster.

This tutorial is a modified version of the original developed by [Kelsey Hightower](https://github.com/kelseyhightower/kubernetes-the-hard-way).
While the original one uses GCP as the platform to deploy kubernetes,  we use a hypervisor to deploy a cluster on a local machine. If you prefer the cloud version, refer to the original one [here](https://github.com/kelseyhightower/kubernetes-the-hard-way)

The results of this tutorial should *not* be viewed as production ready, and may receive limited support from the community, but don't let that stop you from learning!<br/>Note that we are only building 2 controlplane nodes here instead of the recommended 3 that `etcd` requires to maintain quorum. This is to save on resources, and simply to show how to load balance across more than one controlplane node.

### <font color="red">Before shouting "Help! It's not working!"</font>

Please note that with this particular challenge, it is all about the minute detail. If you miss _one tiny step_ anywhere along the way, it's going to break!

Note also that in developing this lab, it has been tested *many many* times! See [how we test it](./docs/99-test.md). Once you have the VMs up and you start to build the cluster, if at any point something isn't working it is 99.9% likely to be because you missed something, not a bug in the lab!

Always run the `cert_verify.sh` script at the places it suggests, and always ensure you are on the correct node when you do stuff. If `cert_verify.sh` shows anything in red, then you have made an error in a previous step. For the controlplane node checks, run the check on `controlplane01` and on `controlplane02`

## Target Audience

The target audience for this tutorial is someone planning to support a production Kubernetes cluster and wants to understand how everything fits together.

## Cluster Details

Kubernetes The Hard Way guides you through bootstrapping a highly available Kubernetes cluster with end-to-end encryption between components and RBAC authentication.

* [Kubernetes](https://github.com/kubernetes/kubernetes) Latest version
* [Container Runtime](https://github.com/containerd/containerd) Latest version
* [Weave Networking](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/)
* [etcd](https://github.com/coreos/etcd) v3.5.9
* [CoreDNS](https://github.com/coredns/coredns) v1.9.4

### Node configuration

We will be building the following:

* Two control plane nodes (`controlplane01` and `controlplane02`) running the control plane components as operating system services. This is not a kubeadm cluster as you are used to if you have been doing the CKA course. The control planes are *not* themselves nodes, therefore will not show with `kubectl get nodes`.
* Two worker nodes (`node01` and `node02`)
* One loadbalancer VM running [HAProxy](https://www.haproxy.org/) to balance requests between the two API servers and provide the endpoint for your KUBECONFIG.

In this lab we will go directly to Vagrant to automate the deployment of VMs in a host platform independent manner, that is, the code here will work for all types of laptops in exactly the same way once the correct prerequisites have been installed.

Note that the [Vagrantfile](vagrant/Vagrantfile) we will use is quite different to what is discussed in any of the course videos. Vagrantfiles are written in the Ruby programming language, therefore any valid Ruby syntax can be used and we take advantage of this fact to make a Vagrantfile that is compatible with all the different types of laptop, and the fact that the laptops can't all use VirtualBox - we must use VMware on Apple Silicon because VirtualBox doesn't work.

Note also that you should not edit the Vagrantfile except in the places suggested in further documents in this lab unless you have a very good understanding of how Vagrant works and of the Ruby programming language, or you will break the deployment! It is specially tuned to make the best use of the CPU and memory resources available on your laptop, dynamcially adjusting the vCPU and RAM of the VMs being created.

## Getting Started

* If you are using Windows or Intel Mac, start [here](./VirtualBox/docs/01-prerequisites.md) to deploy VirtualBox and Vagrant.
* If you are using Apple Silicon Mac (M1/M2/M3), start [here](./apple-silicon/docs/01-prerequisites.md) to deploy VMware Fusion and Vagrant.

