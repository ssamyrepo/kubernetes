### **Kubernetes Deployment **

---

### **1. Prerequisites**
Before starting, ensure you have the following:
- **Kubernetes Cluster**: Use Minikube, Docker Desktop, or a cloud provider (e.g., GKE, EKS, AKS).
- **kubectl**: Install and configure `kubectl` to interact with your cluster.
- **Basic Kubernetes Knowledge**: Familiarity with Pods, ReplicaSets, and YAML files.

---

### **2. Understanding Kubernetes Deployments**
- **Objective**:  how Deployments manage ReplicaSets and Pods.
- **Key Concepts**:
  - **Pod**: The smallest deployable unit in Kubernetes, running one or more containers.
  - **ReplicaSet**: Ensures a specified number of Pod replicas are running.
  - **Deployment**: A higher-level abstraction that manages ReplicaSets and enables declarative updates.

---

### **3. Create a Deployment YAML File**
- **Objective**: Define a Deployment using a YAML manifest.
- **Steps**:
  1. Create a file named `nginx-deployment.yaml`:
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: nginx-deployment
       labels:
         environment: test
     spec:
       replicas: 3
       selector:
         matchLabels:
           environment: test
       template:
         metadata:
           labels:
             environment: test
         spec:
           containers:
           - name: nginx
             image: nginx:1.16
             ports:
             - containerPort: 80
       strategy:
         type: RollingUpdate
         rollingUpdate:
           maxSurge: 1
           maxUnavailable: 0
     ```
  2. Key Sections Explained:
     - **`replicas: 3`**: Ensures 3 Pods are running.
     - **`selector`**: Matches Pods with the label `environment: test`.
     - **`template`**: Defines the Pod template.
     - **`strategy`**: Configures rolling updates:
       - **`maxSurge: 1`**: Allows 1 extra Pod during updates.
       - **`maxUnavailable: 0`**: Ensures no Pods are unavailable during updates.

---

### **4. Apply the Deployment**
- **Objective**: Deploy the Nginx application using the YAML file.
- **Steps**:
  1. Apply the Deployment:
     ```bash
     kubectl apply -f nginx-deployment.yaml
     ```
  2. Verify the Deployment, ReplicaSet, and Pods:
     ```bash
     kubectl get deployments
     kubectl get replicasets
     kubectl get pods
     ```
  3. Describe the Deployment:
     ```bash
     kubectl describe deployment nginx-deployment
     ```

---

### **5. Test High Availability**
- **Objective**: Verify that the ReplicaSet maintains the desired number of Pods.
- **Steps**:
  1. Delete a Pod:
     ```bash
     kubectl delete pod <pod-name>
     ```
  2. Observe the ReplicaSet creating a new Pod:
     ```bash
     kubectl get pods --watch
     ```

---

### **6. Test Deployment Self-Healing**
- **Objective**: Verify that the Deployment restores the ReplicaSet if deleted.
- **Steps**:
  1. Delete the ReplicaSet:
     ```bash
     kubectl delete replicaset <replicaset-name>
     ```
  2. Observe the Deployment recreating the ReplicaSet:
     ```bash
     kubectl get replicasets --watch
     ```

---

### **7. Perform a Rolling Update**
- **Objective**: Update the Nginx image from `1.16` to `1.17` using a rolling update.
- **Steps**:
  1. Edit the Deployment YAML file:
     ```yaml
     spec:
       containers:
       - name: nginx
         image: nginx:1.17
     ```
  2. Apply the updated YAML:
     ```bash
     kubectl apply -f nginx-deployment.yaml
     ```
  3. Observe the rolling update:
     ```bash
     kubectl get pods --watch
     ```
  4. Verify the new image version:
     ```bash
     kubectl describe pod <pod-name>
     ```

---

### **8. Clean Up**
- **Objective**: Delete the Deployment and associated resources.
- **Steps**:
  1. Delete the Deployment:
     ```bash
     kubectl delete -f nginx-deployment.yaml
     ```
  2. Verify all resources are deleted:
     ```bash
     kubectl get deployments
     kubectl get replicasets
     kubectl get pods
     ```

---

### **9. Key Takeaways**
- **Deployments** provide declarative updates for Pods and ReplicaSets.
- **Rolling Updates** ensure zero downtime during application updates.
- **Labels and Selectors** are critical for managing resources in Kubernetes.

---

### **10. Additional Resources**
- **Kubernetes Documentation**: [https://kubernetes.io/docs/](https://kubernetes.io/docs/)
- **Kubectl Cheat Sheet**: [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- **Minikube Documentation**: [https://minikube.sigs.k8s.io/docs/](https://minikube.sigs.k8s.io/docs/)
