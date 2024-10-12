# Running a Stateless Application Using Kubernetes Deployment

This project demonstrates how to deploy and manage a stateless application (NGINX) on Kubernetes using `kubectl` commands and YAML configuration files.

## Objectives

- Create an NGINX deployment using `kubectl`.
- Display information about the deployment.
- Update the NGINX deployment.
- Scale the deployment by increasing the replica count.
- Delete the deployment when finished.

---

## Prerequisites

- **Kubernetes Cluster**: You should have a running Kubernetes cluster. If you don’t have one, you can use Minikube or a Kubernetes playground like [Killercoda](https://killercoda.com) or [Play with Kubernetes](https://labs.play-with-k8s.com/).
  
- **kubectl**: Ensure that the `kubectl` command-line tool is installed and configured to interact with your Kubernetes cluster. To check if `kubectl` is configured, use:

    ```bash
    kubectl version
    ```

---

## Step 1: Create an NGINX Deployment

### Command-Line Approach

You can create an NGINX Deployment by running the following `kubectl` command:

```bash
kubectl create deployment nginx-deploy --image=nginx:1.14.2
```

This command will create an NGINX deployment using the image `nginx:1.14.2` with the default number of replicas (1).

### YAML File Approach

Alternatively, you can define the deployment in a YAML file and apply it to the cluster. Below is the `deployment.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Apply this deployment by running:

```bash
kubectl apply -f deployment.yaml
```

---

## Step 2: Display Deployment Information

Once the deployment is created, you can display information about it by running:

```bash
kubectl describe deployment nginx-deployment
```

This command will display details about the deployment, such as the number of replicas, labels, pod template, and events.

---

## Step 3: List the Pods Created by the Deployment

To list the Pods created by the deployment, use the following command:

```bash
kubectl get pods -l app=nginx
```

This will display the status of the Pods, showing whether they are running.

---

## Step 4: Updating the NGINX Deployment

To update the NGINX deployment and change the image version, modify the deployment YAML file as follows:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1 # Update the version from 1.14.2 to 1.16.1
        ports:
        - containerPort: 80
```

Apply the updated YAML file to the cluster:

```bash
kubectl apply -f deployment.yaml
```

---

## Step 5: Scaling the Application

To scale the deployment and increase the number of replicas, update the `replicas` field in the YAML file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 4 # Increase the number of replicas from 2 to 4
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1
        ports:
        - containerPort: 80
```

Apply the changes:

```bash
kubectl apply -f deployment.yaml
```

Check if the deployment has scaled to 4 replicas:

```bash
kubectl get pods -l app=nginx
```

---

## Step 6: Deleting the Deployment

When you're done with the deployment, you can delete it using the following command:

```bash
kubectl delete deployment nginx-deployment
```

This will delete the deployment along with all the Pods associated with it.

---

## Additional Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)

---

This project is a hands-on introduction to managing stateless applications in Kubernetes. As you progress, you can explore more advanced features like StatefulSets, Services, and Ingress controllers. Happy learning!

--- 

