# Run a Single-Instance Stateful Application on Kubernetes

This project demonstrates how to run a stateful application (MySQL) in Kubernetes using a **PersistentVolume** and a **Deployment**. The application uses persistent storage to ensure data is retained even if the Pod is deleted or replaced.

## Objectives

1. Create a **PersistentVolume** (PV) for storage.
2. Create a **MySQL Deployment** that references the PersistentVolumeClaim (PVC).
3. Expose MySQL within the Kubernetes cluster using a Service.
4. Interact with the MySQL instance using a MySQL client.
5. Understand best practices for handling stateful applications in Kubernetes.

---

## Prerequisites

- **Kubernetes Cluster**: You must have access to a Kubernetes cluster. This tutorial can be run on a local cluster using Minikube or any other Kubernetes playground, such as:
  - [Killercoda](https://killercoda.com/)
  - [Play with Kubernetes](https://labs.play-with-k8s.com/)
  
- **kubectl**: Ensure the `kubectl` command-line tool is installed and configured to communicate with your Kubernetes cluster. Check the version by running:
  
    ```bash
    kubectl version
    ```

- **PersistentVolume Provisioner**: Either a dynamic PersistentVolume provisioner (with a default StorageClass) or a manually provisioned PersistentVolume.

---

## Step 1: Deploy MySQL with Persistent Storage

### PersistentVolume and PersistentVolumeClaim

You need a PersistentVolume (PV) and a PersistentVolumeClaim (PVC) to store MySQL data. Below is the YAML for creating a 20Gi PersistentVolume and its corresponding PersistentVolumeClaim.

```yaml
# mysql-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

Apply the PV and PVC YAML file:

```bash
kubectl apply -f mysql-pv.yaml
```

### MySQL Deployment

The following YAML defines the MySQL deployment, which references the PersistentVolumeClaim for persistent storage:

```yaml
# mysql-deployment.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
    - port: 3306
  selector:
    app: mysql
  clusterIP: None
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - image: mysql:5.6
          name: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: password # Insecure; use secrets in production
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
```

Apply the MySQL deployment:

```bash
kubectl apply -f mysql-deployment.yaml
```

---

## Step 2: Inspecting and Accessing MySQL

### View the MySQL Deployment

To get information about the MySQL deployment, run:

```bash
kubectl describe deployment mysql
```

### List Pods Created by the Deployment

To see the MySQL Pod running, use:

```bash
kubectl get pods -l app=mysql
```

The output should show the MySQL Pod is running:

```bash
NAME                   READY   STATUS    RESTARTS   AGE
mysql-xxxxxx-yyyyyy    1/1     Running   0          3m
```

### Inspect PersistentVolumeClaim

Check the details of the PersistentVolumeClaim:

```bash
kubectl describe pvc mysql-pv-claim
```

You should see that the claim is **Bound** and linked to the `mysql-pv-volume`.

---

## Step 3: Access the MySQL Instance

You can connect to MySQL from within the cluster using the following command, which runs a MySQL client in a temporary Pod:

```bash
kubectl run -it --rm --image=mysql:5.6 --restart=Never mysql-client -- mysql -h mysql -ppassword
```

If the connection is successful, you'll see the MySQL prompt:

```bash
mysql>
```

---

## Step 4: Updating the MySQL Deployment

### Update the Deployment YAML

When updating a stateful app, it’s important to use a **Recreate** strategy to prevent multiple instances of the app from running simultaneously. Here's an example update:

```yaml
strategy:
  type: Recreate
```

To apply updates, run:

```bash
kubectl apply -f mysql-deployment.yaml
```

---

## Step 5: Deleting the Deployment

To clean up the deployment and associated resources, run the following commands:

```bash
kubectl delete deployment,svc mysql
kubectl delete pvc mysql-pv-claim
kubectl delete pv mysql-pv-volume
```

---

## Best Practices

- For sensitive data like passwords, use **Kubernetes Secrets** instead of environment variables.
- Ensure the PersistentVolume is adequately provisioned before running the application.
- Do not scale single-instance stateful applications. If multiple instances are needed, consider using **StatefulSets**.

---

## Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

---

This project demonstrates the basics of running a stateful application on Kubernetes using persistent storage. Happy deploying!

---