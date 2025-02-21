### **Microservices, Docker, and Kubernetes**

---

### **1. Prerequisites**
Before starting   ensure you have the following installed:
- **Docker**: [Install Docker](https://docs.docker.com/get-docker/)
- **Minikube**: [Install Minikube](https://minikube.sigs.k8s.io/docs/start/)
- **kubectl**: [Install kubectl](https://kubernetes.io/docs/tasks/tools/)
- **Node.js** (optional, for creating a sample app): [Install Node.js](https://nodejs.org/)

---

### **2. Understanding Microservices**
- **Objective**: Learn the difference between monolithic and microservices architectures.
- **Steps**:
  1. Watch the video segment (00:01 - 05:55) to understand microservices.
  2. Compare monolithic vs. microservices architecture:
     - Monolithic: Single application with UI, business logic, and database.
     - Microservices: Independent services communicating via HTTP APIs.
  3. Discuss advantages (scalability, fault isolation) and disadvantages (complexity, overhead).

---

### **3. Introduction to Docker**
- **Objective**: Learn how to containerize applications using Docker.
- **Steps**:
  1. Watch the video segment (05:55 - 11:28) to understand Docker.
  2. Install Docker if not already installed.
  3. Run a simple Docker container:
     ```bash
     docker run hello-world
     ```
  4. Run a web server container:
     ```bash
     docker run -p 3000:80 tutum/hello-world
     ```
     - Access the web server at `http://localhost:3000`.
  5. Run multiple instances of the container on different ports:
     ```bash
     docker run -d -p 3001:80 tutum/hello-world
     docker run -d -p 3002:80 tutum/hello-world
     ```
  6. List running containers:
     ```bash
     docker ps
     ```
  7. Stop and remove containers:
     ```bash
     docker kill <container_id>
     docker rm <container_id>
     ```

---

### **4. Creating a Docker Image**
- **Objective**: Create a custom Docker image for a Node.js application.
- **Steps**:
  1. Watch the video segment (36:40 - 47:42) to understand Dockerfile creation.
  2. Create a Node.js application:
     - Initialize a new Node.js project:
       ```bash
       mkdir myapp
       cd myapp
       npm init -y
       ```
     - Install Express:
       ```bash
       npm install express
       ```
     - Create `index.js`:
       ```javascript
       const express = require('express');
       const app = express();
       const port = 8080;

       app.get('/', (req, res) => {
           res.send('Hello World!');
       });

       app.listen(port, () => {
           console.log(`App running on port ${port}`);
       });
       ```
  3. Create a `Dockerfile`:
     ```dockerfile
     FROM node:14
     WORKDIR /usr/src/app
     COPY package*.json ./
     RUN npm install
     COPY . .
     EXPOSE 8080
     CMD ["node", "index.js"]
     ```
  4. Build the Docker image:
     ```bash
     docker build -t myapp:v1 .
     ```
  5. Run the Docker container:
     ```bash
     docker run -p 8080:8080 myapp:v1
     ```
  6. Access the app at `http://localhost:8080`.

---

### **5. Introduction to Kubernetes**
- **Objective**: Deploy and manage containers using Kubernetes.
- **Steps**:
  1. Watch the video segment (11:28 - 19:11) to understand Kubernetes.
  2. Start Minikube:
     ```bash
     minikube start
     ```
  3. Verify Minikube and kubectl:
     ```bash
     kubectl get nodes
     ```
  4. Create a Kubernetes deployment and service:
     - Create a `deployment.yaml` file:
       ```yaml
       apiVersion: apps/v1
       kind: Deployment
       metadata:
         name: myapp-deployment
       spec:
         replicas: 3
         selector:
           matchLabels:
             app: myapp
         template:
           metadata:
             labels:
               app: myapp
           spec:
             containers:
             - name: myapp
               image: myapp:v1
               ports:
               - containerPort: 8080
       ---
       apiVersion: v1
       kind: Service
       metadata:
         name: myapp-service
       spec:
         type: LoadBalancer
         ports:
         - port: 8080
           targetPort: 8080
           nodePort: 30001
         selector:
           app: myapp
       ```
  5. Apply the deployment:
     ```bash
     kubectl apply -f deployment.yaml
     ```
  6. Verify the deployment:
     ```bash
     kubectl get pods
     kubectl get deployments
     kubectl get services
     ```
  7. Access the app:
     - Get the Minikube IP:
       ```bash
       minikube ip
       ```
     - Access the app at `http://<minikube-ip>:30001`.

---

### **6. Scaling and Load Balancing**
- **Objective**: Learn how to scale applications in Kubernetes.
- **Steps**:
  1. Watch the video segment (34:07 - 35:40) to understand scaling.
  2. Scale the deployment:
     ```bash
     kubectl scale deployment myapp-deployment --replicas=5
     ```
  3. Verify scaling:
     ```bash
     kubectl get pods
     ```
  4. Test load balancing by refreshing the app URL multiple times.

---

### **7. Cleanup**
- **Objective**: Clean up resources after the lab.
- **Steps**:
  1. Delete the deployment and service:
     ```bash
     kubectl delete -f deployment.yaml
     ```
  2. Stop Minikube:
     ```bash
     minikube stop
     ```
  3. Remove Docker containers and images:
     ```bash
     docker rm -f $(docker ps -aq)
     docker rmi myapp:v1
     ```

---

### **8. Additional Resources**
- **Docker Documentation**: [https://docs.docker.com/](https://docs.docker.com/)
- **Kubernetes Documentation**: [https://kubernetes.io/docs/](https://kubernetes.io/docs/)
- **Minikube Documentation**: [https://minikube.sigs.k8s.io/docs/](https://minikube.sigs.k8s.io/docs/)

---

