### **Script: `kubectl-quick-reference.sh`**

```bash
#!/bin/bash

# Kubectl Quick Reference Script for Ubuntu
# This script includes commands for setting up kubectl autocomplete, managing Kubernetes resources, and interacting with the cluster.

# Ensure kubectl is installed
if ! command -v kubectl &> /dev/null; then
    echo "kubectl could not be found. Please install kubectl first."
    exit 1
fi

# Set up kubectl autocomplete for bash
echo "Setting up kubectl autocomplete for bash..."
source <(kubectl completion bash)  # Enable autocomplete in the current shell
echo "source <(kubectl completion bash)" >> ~/.bashrc  # Enable autocomplete permanently

# Create a shorthand alias for kubectl
echo "Creating alias 'k' for kubectl..."
alias k=kubectl
complete -o default -F __start_kubectl k
echo "alias k=kubectl" >> ~/.bashrc
echo "complete -o default -F __start_kubectl k" >> ~/.bashrc

# Kubectl context and configuration
echo "Kubectl context and configuration commands:"
kubectl config view  # Show merged kubeconfig settings
kubectl config get-contexts  # Display list of contexts
kubectl config current-context  # Display the current context
kubectl config use-context my-cluster-name  # Set the default context to my-cluster-name

# Kubectl apply commands
echo "Kubectl apply commands:"
kubectl apply -f ./my-manifest.yaml  # Create resource(s) from a manifest file
kubectl apply -f ./dir  # Create resource(s) from all manifest files in a directory
kubectl apply -f https://example.com/manifest.yaml  # Create resource(s) from a URL

# Viewing and finding resources
echo "Viewing and finding resources:"
kubectl get services  # List all services in the namespace
kubectl get pods --all-namespaces  # List all pods in all namespaces
kubectl get pods -o wide  # List all pods in the current namespace with more details
kubectl describe nodes my-node  # Describe a specific node
kubectl describe pods my-pod  # Describe a specific pod

# Updating resources
echo "Updating resources:"
kubectl set image deployment/frontend www=image:v2  # Rolling update for a deployment
kubectl rollout history deployment/frontend  # Check the history of deployments
kubectl rollout undo deployment/frontend  # Rollback to the previous deployment
kubectl rollout status -w deployment/frontend  # Watch rolling update status

# Patching resources
echo "Patching resources:"
kubectl patch node k8s-node-1 -p '{"spec":{"unschedulable":true}}'  # Partially update a node
kubectl patch pod valid-pod -p '{"spec":{"containers":[{"name":"kubernetes-serve-hostname","image":"new image"}]}}'  # Update a container's image

# Deleting resources
echo "Deleting resources:"
kubectl delete -f ./pod.json  # Delete a pod using the type and name specified in pod.json
kubectl delete pod unwanted --now  # Delete a pod with no grace period
kubectl delete pods,services -l name=myLabel  # Delete pods and services with a specific label

# Interacting with running Pods
echo "Interacting with running Pods:"
kubectl logs my-pod  # Dump pod logs (stdout)
kubectl logs -f my-pod  # Stream pod logs (stdout)
kubectl exec my-pod -- ls /  # Run a command in an existing pod
kubectl exec --stdin --tty my-pod -- /bin/sh  # Interactive shell access to a running pod

# Copying files and directories to and from containers
echo "Copying files and directories:"
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir  # Copy a local directory to a remote pod
kubectl cp my-pod:/tmp/foo /tmp/bar  # Copy a file from a remote pod to locally

# Interacting with Deployments and Services
echo "Interacting with Deployments and Services:"
kubectl logs deploy/my-deployment  # Dump Pod logs for a Deployment
kubectl port-forward svc/my-service 5000  # Forward a local port to a Service

# Interacting with Nodes and cluster
echo "Interacting with Nodes and cluster:"
kubectl cordon my-node  # Mark a node as unschedulable
kubectl drain my-node  # Drain a node in preparation for maintenance
kubectl uncordon my-node  # Mark a node as schedulable
kubectl top node  # Show metrics for all nodes

# Resource types
echo "Resource types:"
kubectl api-resources  # List all supported resource types

# Formatting output
echo "Formatting output:"
kubectl get pods -o wide  # Output in plain-text format with additional information
kubectl get pods -o json  # Output a JSON formatted API object
kubectl get pods -o yaml  # Output a YAML formatted API object

# Kubectl output verbosity and debugging
echo "Kubectl output verbosity and debugging:"
kubectl get pods --v=6  # Display requested resources
kubectl get pods --v=7  # Display HTTP request headers
kubectl get pods --v=8  # Display HTTP request contents

echo "Kubectl quick reference script completed!"
```

---

### **How to Use the Script**

1. **Save the Script**:
   - Save the script as `kubectl-quick-reference.sh`.

2. **Make the Script Executable**:
   - Run the following command to make the script executable:
     ```bash
     chmod +x kubectl-quick-reference.sh
     ```

3. **Run the Script**:
   - Execute the script:
     ```bash
     ./kubectl-quick-reference.sh
     ```

4. **Verify**:
   - The script will execute all the `kubectl` commands and set up autocomplete for `kubectl`.

