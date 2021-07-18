## Virtualization Driver

#### KVM (Kernel-based Virtual Machine)

[KVM (Kernel-based Virtual Machine)](https://www.linux-kvm.org/page/Main_Page) is a full virtualization solution for Linux on x86 hardware containing virtualization extensions. To work with KVM, minikube uses the [libvirt virtualization API](https://libvirt.org/)

##### Requirements

- libvirt v1.3.1 or higher
- qemu-kvm v2.0 or higher

##### Usage

Start a cluster using the kvm2 driver:

```shell
minikube start --driver=kvm2
```

To make kvm2 the default driver:

```bash
minikube config set driver kvm2
```





## Minikube

##### Start

```bash
minikube start
```

##### Pause

```shell
minikube pause
```

##### Halt the cluster

```shell
minikube stop
```

##### Increase the default memory limit (requires a restart):

```shell
minikube config set memory 16384
```

##### Browse the catalog of easily installed Kubernetes services:

```shell
minikube addons list
```

##### Dashboard

```bash
minikube dashboard --url
```

##### Delete

```shell
minikube delete --all
```



## Kubectl

##### Remote

```bash
cat $HOME/.kube/config
```

