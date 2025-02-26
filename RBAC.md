## **Introduction**
 **Kubernetes Role-Based Access Control (RBAC)** by implementing secure access control for a **Grade Submission API**. This will guide you through setting up **Service Accounts, Cluster Roles, Role Bindings**, and token authentication to ensure only authorized clients can access resources.

---

## **Step 1: Clone the Repository**
Start by cloning the tutorial repository that contains the necessary starter code.

1. Copy the repository URL from the video description.
2. Open a terminal and navigate to your desired directory.
3. Clone the repository using:
   ```bash
   git clone <repository-url>
   ```
4. Navigate into the cloned directory:
   ```bash
   cd <repository-directory>
   ```
5. Open the project in **Visual Studio Code** or any preferred editor.

---

## **Step 2: Understand the API Deployment**
The repository includes Kubernetes manifests defining:
- A **namespace** called `grade-demo`.
- A **Grade Submission API** deployment.
- A **NodePort service** that exposes the API externally on port `31000`.

### **Apply the Configuration**
Deploy the API and service using:
```bash
kubectl apply -f .
```
Verify that the API is running:
```bash
kubectl get pods -n grade-demo
```

---

## **Step 3: Test the API**
Use `curl` to interact with the API.

### **Add grades (POST request)**
```bash
curl -X POST http://localhost:31000/grades -d '{"name":"Harry","score":85}' -H "Content-Type: application/json"
```

### **Retrieve grades (GET request)**
```bash
curl -X GET http://localhost:31000/grades
```

**Issue Identified**:  
Any client can access the API **without authentication**. This exposes the API to unauthorized access.

---

## **Step 4: Implement Secure Access Control**
To restrict access, we will introduce a **secure validation proxy** that enforces RBAC.

### **Modify the API Deployment**
We will add a **sidecar container** named `kube-rbac-proxy`, which will:
- Validate incoming requests before they reach the API.
- Check authentication against the Kubernetes API.

#### **Modify the Deployment YAML**
1. Add a **sidecar container** to the `grade-submission-api` pod:
   ```yaml
   containers:
   - name: kube-rbac-proxy
     image: quay.io/brancz/kube-rbac-proxy
     args:
       - --secure-listen-address=0.0.0.0:8443
       - --upstream=http://127.0.0.1:3000/
       - --logtostderr=true
       - --v=2
   ports:
   - containerPort: 8443
     name: https
   ```

2. Modify the **Service** to route requests through the proxy:
   ```yaml
   spec:
     ports:
     - port: 31000
       targetPort: https
   ```

3. Apply the updated configuration:
   ```bash
   kubectl apply -f .
   ```

### **Test Secure Access**
Try accessing the API again:
```bash
curl -k -X GET https://localhost:31000/grades
```
**Expected Result**:  
`Unauthorized` – This confirms that access is now restricted.

---

## **Step 5: Create a Service Account & Token**
To authenticate API requests, we need a **Service Account** and a corresponding **token**.

### **Define the Service Account**
Create a `service-account.yaml` file:
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: grade-service-account
  namespace: grade-demo
```
Apply the configuration:
```bash
kubectl apply -f service-account.yaml
```

### **Generate a Token for the Service Account**
1. Create a **Secret** to hold the token:
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: grade-sa-token
     namespace: grade-demo
     annotations:
       kubernetes.io/service-account.name: grade-service-account
   type: kubernetes.io/service-account-token
   ```
2. Apply the configuration:
   ```bash
   kubectl apply -f secret.yaml
   ```

3. Retrieve the token:
   ```bash
   TOKEN=$(kubectl get secret grade-sa-token -n grade-demo -o jsonpath='{.data.token}' | base64 --decode)
   ```

---

## **Step 6: Assign RBAC Permissions**
Currently, the service account does not have permissions to access the API.

### **Create a Role and Role Binding**
1. Define a **ClusterRole** (`cluster-role.yaml`):
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     name: grade-service-role
   rules:
   - apiGroups: [""]
     resources: ["pods", "services"]
     verbs: ["get", "list"]
   - nonResourceURLs: ["/grades"]
     verbs: ["get", "post"]
   ```

2. Bind the role to the service account (`role-binding.yaml`):
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     name: grade-service-binding
     namespace: grade-demo
   subjects:
   - kind: ServiceAccount
     name: grade-service-account
     namespace: grade-demo
   roleRef:
     kind: ClusterRole
     name: grade-service-role
     apiGroup: rbac.authorization.k8s.io
   ```

3. Apply the RBAC configuration:
   ```bash
   kubectl apply -f cluster-role.yaml
   kubectl apply -f role-binding.yaml
   ```

---

## **Step 7: Authenticate with Token**
Now, use the token to make an authenticated request:

```bash
curl -k -H "Authorization: Bearer $TOKEN" https://localhost:31000/grades
```

### **Expected Result**  
A successful response with authorized access.

---

## **Step 8: Secure the Proxy's Permissions**
The `kube-rbac-proxy` requires additional permissions to verify tokens.

### **Create a Role for Token Reviews**
Define `proxy-role.yaml`:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: proxy-service-role
rules:
- apiGroups: ["authentication.k8s.io"]
  resources: ["tokenreviews"]
  verbs: ["create"]
- apiGroups: ["authorization.k8s.io"]
  resources: ["subjectaccessreviews"]
  verbs: ["create"]
```

### **Bind the Role to the Proxy Service Account**
Define `proxy-binding.yaml`:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: proxy-service-binding
  namespace: grade-demo
subjects:
- kind: ServiceAccount
  name: grade-service-account
  namespace: grade-demo
roleRef:
  kind: ClusterRole
  name: proxy-service-role
  apiGroup: rbac.authorization.k8s.io
```

### **Apply the Changes**
```bash
kubectl apply -f proxy-role.yaml
kubectl apply -f proxy-binding.yaml
```

---

## **Final Step: Test Again**
Try accessing the API again:
```bash
curl -k -H "Authorization: Bearer $TOKEN" https://localhost:31000/grades
```

### **Expected Result**  
The request should now be **authorized** and return valid data.

---
