# com.plutozone.knowledge.middleware.Kubernetes


## Software
- Virtual Box and 4VM(1 for Docker + 3 for Kubernetes Cluster)
- Rocky
- MobaXterm
- Visual Studio Code for Yaml and Extentions(Kubernetes + Kubernetes Support + Remote SSH)


## Overview at https://kubernetes.io/ko/docs/concepts/overview/
- Container Cluster = Container Orchestrator = Kubernetes
- Kubernetes(k8s)
- control-plane=master


## Installation
### master@192.168.56.10 + node1@192.168.56.11 + node2@192.168.56.12

### docker@192.168.56.100
- install Docker at Rocky or Ubuntu

### Kube Command(kubuctl) for Windows
- https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-windows/#install-nonstandard-package-tools
- copy to %USER%.kube\config from ~/.kube/config
- C:\kubectl get node


## NONE Ready(reset, rm and reconfig)
```bash
$ kubeadm reset		# at master/node1/node2
$ rm -Ff .kube		# at master
```
