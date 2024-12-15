Here are **20 scenario-based Kubernetes interview questions and answers** that will help you prepare for a Kubernetes-focused interview:

---

### 1. **Scenario**: *You need to deploy an application in Kubernetes. How would you do that using a YAML file?*

**Answer**:
You can create a `deployment.yaml` file to define your deployment. Here’s an example for deploying an application:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
        ports:
        - containerPort: 8080
```

To apply this configuration:
```bash
kubectl apply -f deployment.yaml
```

This deploys a set of 3 replicas of the application.

---

### 2. **Scenario**: *You need to check the logs of a pod running in Kubernetes. How would you do this?*

**Answer**:
You can use the `kubectl logs` command to view the logs of a pod:

```bash
kubectl logs <pod_name>
```

If the pod has multiple containers, you need to specify the container name:
```bash
kubectl logs <pod_name> -c <container_name>
```

To view the logs of a specific pod from a particular namespace, use:
```bash
kubectl logs <pod_name> -n <namespace>
```

---

### 3. **Scenario**: *You want to update the image of a running deployment without downtime. How would you do that?*

**Answer**:
You can use `kubectl set image` to update the deployment image:

```bash
kubectl set image deployment/my-app-deployment my-app-container=my-app-image:v2
```

This will trigger a rolling update to apply the new image to the running pods without downtime. Kubernetes will ensure that some pods are always running while others are being updated.

---

### 4. **Scenario**: *A pod in your Kubernetes cluster is stuck in `Pending` state. How would you troubleshoot this?*

**Answer**:
To troubleshoot a pod stuck in the `Pending` state, follow these steps:
1. Check the pod description for more details:
   ```bash
   kubectl describe pod <pod_name>
   ```
   Look for events that can explain why the pod is pending, such as insufficient resources or unsatisfiable node selectors.

2. Check if there are available resources (CPU, memory) in your cluster:
   ```bash
   kubectl describe nodes
   ```

3. Verify if the pod is correctly configured with the right resource requests and limits.

4. If using `kubectl get pod` shows `No resources found`, it could indicate a scheduling issue.

---

### 5. **Scenario**: *You want to expose a Kubernetes application on port 80 of the host machine. How would you do that using a `Service`?*

**Answer**:
You can create a `Service` of type `NodePort` or `LoadBalancer` to expose the application. Here’s an example of exposing the service via a `NodePort`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30080
  type: NodePort
```

This service maps port 80 of the cluster to port 30080 on the host.

To apply this:
```bash
kubectl apply -f service.yaml
```

---

### 6. **Scenario**: *You need to scale the number of replicas for a deployment. How would you scale up the deployment?*

**Answer**:
You can scale the deployment by using the `kubectl scale` command:

```bash
kubectl scale deployment my-app-deployment --replicas=5
```

This will scale the deployment to 5 replicas. To verify the scaling:
```bash
kubectl get deployments
```

---

### 7. **Scenario**: *You need to run a job that processes a batch task in Kubernetes. How would you define a job to run once?*

**Answer**:
You can define a Kubernetes `Job` resource in a YAML file. Here’s an example of a job that runs once:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-job
spec:
  template:
    spec:
      containers:
      - name: batch-task
        image: my-batch-task-image
      restartPolicy: Never
```

To apply the job:
```bash
kubectl apply -f job.yaml
```

This will create a pod that runs the batch job and exits when completed.

---

### 8. **Scenario**: *You have a pod running multiple containers. How do you inspect one of the containers inside that pod?*

**Answer**:
You can use the `kubectl exec` command to interact with a specific container in the pod. First, get the pod name:
```bash
kubectl get pods
```

Then, run a shell on the container:
```bash
kubectl exec -it <pod_name> -c <container_name> -- /bin/bash
```

This opens a shell to the specified container inside the pod.

---

### 9. **Scenario**: *You want to delete a namespace in your Kubernetes cluster. How would you do that?*

**Answer**:
To delete a namespace, use:
```bash
kubectl delete namespace <namespace_name>
```

This will delete all resources (pods, services, deployments, etc.) within that namespace.

---

### 10. **Scenario**: *You need to deploy an application in a Kubernetes cluster, but the deployment needs to have persistent storage. How do you achieve this?*

**Answer**:
You can use `PersistentVolume` (PV) and `PersistentVolumeClaim` (PVC) to manage storage. Here’s an example YAML for defining a `PVC`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

In your deployment, you would reference the PVC:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: my-app-container
        image: my-app-image
        volumeMounts:
        - name: storage
          mountPath: /data
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: my-pvc
```

---

### 11. **Scenario**: *You want to ensure that only one pod of a particular deployment runs at a time. How do you achieve this?*

**Answer**:
To ensure only one pod runs at a time, you can set the `replicas` field of your deployment to `1` or use a `StatefulSet` with the `replicaCount` set to `1`. Here's an example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image
```

This ensures that only one pod of the deployment is running.

---

### 12. **Scenario**: *You need to troubleshoot why a deployment isn’t starting up correctly. What steps would you take?*

**Answer**:
To troubleshoot a deployment issue:
1. Check the pod’s status:
   ```bash
   kubectl get pods
   ```

2. Describe the pod to get more details:
   ```bash
   kubectl describe pod <pod_name>
   ```

3. Check for events related to the pod:
   ```bash
   kubectl get events --sort-by='.metadata.creationTimestamp'
   ```

4. Inspect the container logs:
   ```bash
   kubectl logs <pod_name> -c <container_name>
   ```

5. Ensure resource requests/limits are not too high or low.

---

### 13. **Scenario**: *You need to run a pod only on nodes with a specific label. How would you do this?*

**Answer**:
You can use node selectors to schedule the pod on nodes with specific labels. Here’s an example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - name: my-app-container
    image: my-app-image
```

This pod will only run on nodes labeled with `disktype=ssd`.

---

### 14. **Scenario**: *You want to perform a canary deployment to test new changes before full rollout. How do you achieve this?*

**Answer**:
A canary deployment can be achieved by using a deployment strategy. You can create a deployment with a small number of replicas for the new version and gradually increase the number of replicas once it’s confirmed stable. Here's an example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec

:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:v2
```

You start with 1 or 2 replicas for the new version and gradually scale to more replicas based on the success of the deployment.

---

### 15. **Scenario**: *You want to ensure that only certain users can access specific resources in Kubernetes. How would you handle this?*

**Answer**:
You can use **Role-Based Access Control (RBAC)** in Kubernetes to control access to resources. For example, you can create a `Role` or `ClusterRole` and bind it to a user using `RoleBinding` or `ClusterRoleBinding`.

Here’s an example of creating a `Role`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: my-namespace
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

Then, bind this role to a user:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: my-namespace
subjects:
- kind: User
  name: "my-user"
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

### 16. **Scenario**: *You want to set up horizontal pod autoscaling in Kubernetes based on CPU utilization. How would you configure it?*

**Answer**:
You can use the `HorizontalPodAutoscaler` (HPA) to automatically scale the number of replicas based on CPU usage. Here’s an example of a YAML file to set up HPA:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```

This will scale the deployment based on CPU usage, keeping it between 1 and 10 replicas.

---

### 17. **Scenario**: *You need to expose a set of microservices to the internet using an ingress controller. How do you configure this?*

**Answer**:
To expose multiple services using an ingress controller, you need to create an ingress resource:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /service1
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
      - path: /service2
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 80
```

Ensure that you have an **Ingress Controller** like **nginx-ingress** running in your cluster.

---

### 18. **Scenario**: *You need to ensure that your pods are always scheduled to run in specific nodes in your Kubernetes cluster. How can you enforce this?*

**Answer**:
You can use **affinity** to control which nodes your pods are scheduled on. Here's an example using **node affinity**:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: my-app-container
    image: my-app-image
```

This ensures the pod is only scheduled on nodes with the label `disktype=ssd`.

---

### 19. **Scenario**: *You need to install Helm in your Kubernetes cluster to manage applications. How would you install Helm?*

**Answer**:
To install Helm, first, you need to install the Helm client on your local machine. Then, initialize Helm in your Kubernetes cluster:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

Once Helm is installed, add a Helm chart repository:
```bash
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

To install a Helm chart:
```bash
helm install my-release stable/nginx-ingress
```

---

### 20. **Scenario**: *Your pods are failing with a `CrashLoopBackOff` status. How would you troubleshoot this?*

**Answer**:
1. First, get the pod logs:
   ```bash
   kubectl logs <pod_name>
   ```

2. Describe the pod to check for events:
   ```bash
   kubectl describe pod <pod_name>
   ```

3. Check the readiness and liveness probes in the pod definition to ensure they are correctly configured.

4. If the application inside the pod is failing to start, you may need to check for application-specific errors or misconfigurations.

5. Check if there are sufficient resources (CPU, memory) allocated to the pod.

--- 

These scenario-based questions should help you prepare for Kubernetes-related interview questions. They cover real-world problems and demonstrate your problem-solving skills and understanding of Kubernetes concepts.
