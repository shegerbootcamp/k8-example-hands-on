# Getting Started with Minikube, Kubectl, and Vagrant

This guide will help you get started with Kubernetes on your local machine using **Minikube**, **kubectl**, and **Vagrant**. You’ll be able to deploy a simple Kubernetes cluster and manage it using `kubectl`.

## Prerequisites

Before starting, ensure that you have the following installed:

1. [Minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download)
2. [Kubectl](https://kubernetes.io/docs/tasks/tools/)
3. [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation)

---

## Installation

### 1. Install Minikube

You can follow the [Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/) based on your operating system. Make sure it's properly set up.

To verify the installation, run:

```bash
minikube version
```

### 2. Install Kubectl

Follow the official [Kubectl installation guide](https://kubernetes.io/docs/tasks/tools/) for your operating system.

Verify installation by running:

```bash
kubectl version --client
```

### 3. Install Vagrant

Visit the [Vagrant installation page](https://www.vagrantup.com/docs/installation) for your OS and follow the steps. 

To verify, run:

```bash
vagrant --version
```

---

## Setting Up Minikube

Once all prerequisites are installed, follow these steps to get Kubernetes up and running.

### 1. Start Minikube

To start a Minikube cluster, run the following command:

```bash
minikube start
```

If you need to use a specific VM driver (like VirtualBox, Hyperkit, etc.), include the `--vm-driver` option. For example:

```bash
minikube start --vm-driver=virtualbox
```

### 2. Check Minikube Status

You can check if your Minikube cluster is running by using:

```bash
minikube status
```

### 3. Point kubectl to Minikube

Minikube automatically sets up your Kubernetes configuration in `~/.kube/config` to point `kubectl` at the Minikube cluster.

To verify that kubectl is properly configured to use Minikube, run:

```bash
kubectl cluster-info
```

### 4. Enable Kubernetes LoadBalancers

To allow Kubernetes to use `LoadBalancer` services in Minikube, run the following command in a separate terminal window:

```bash
minikube tunnel
```

This will allow Minikube to create `LoadBalancer` services locally.

---

## Basic Usage

Once Minikube is running, you can deploy your first Kubernetes application.

1. **Create a simple deployment** (e.g., an NGINX pod):

   ```bash
   kubectl create deployment nginx-deploy --image=nginx
   ```

2. **Check the status of the deployment**:

   ```bash
   kubectl get deployments
   kubectl get pods
   ```

3. **Expose the deployment via a service**:

   ```bash
   kubectl expose deployment nginx-deploy --type=LoadBalancer --port=80
   ```

4. **Access the service**:

   You can access the NGINX deployment through the `LoadBalancer` service. Check for the IP and port by running:

   ```bash
   kubectl get services
   ```

   If you're using `minikube tunnel`, you should be able to visit the service using `http://<external-ip>:<port>`.

5. **Clean up the resources**:

   After you're done, you can clean up by running:

   ```bash
   kubectl delete service nginx-deploy
   kubectl delete deployment nginx-deploy
   ```

---

## Using Vagrant with Minikube

If you'd like to set up a Kubernetes cluster using Vagrant, follow these steps:

1. Create a **Vagrantfile** with the necessary Minikube configuration. For example:

   ```ruby
   Vagrant.configure("2") do |config|
     config.vm.box = "bento/ubuntu-20.04"
     config.vm.provider "virtualbox" do |v|
       v.memory = 4096
       v.cpus = 2
     end
   end
   ```

2. Start the virtual machine using Vagrant:

   ```bash
   vagrant up
   ```

3. SSH into the Vagrant machine:

   ```bash
   vagrant ssh
   ```

4. Install **Minikube** and **kubectl** inside the Vagrant VM if they are not already installed, then follow the same steps outlined above to start Minikube and manage your Kubernetes cluster.

---

## Troubleshooting

- **Minikube Failed to Start**: Make sure your system meets the memory and CPU requirements. If using a VM driver, ensure the appropriate virtualization software (e.g., VirtualBox, HyperKit) is installed.
- **kubectl Commands Not Working**: Verify that your `~/.kube/config` is correctly pointing to the Minikube cluster by running `kubectl config current-context`.
- **Accessing LoadBalancer Services**: Ensure `minikube tunnel` is running in a separate terminal for services that use the `LoadBalancer` type.

---

## Resources

- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/handbook/controls/)
- [Kubectl Documentation](https://kubernetes.io/docs/reference/kubectl/)
- [Vagrant Documentation](https://www.vagrantup.com/docs)
