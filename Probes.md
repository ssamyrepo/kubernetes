
1. **Why Probes are Needed**:
   - Kubernetes restarts a pod if it detects that the pod is unhealthy.
   - By default, Kubernetes only checks if the main process of the container is running. It doesn’t check the internal functionality of the application.
   - Probes allow Kubernetes to perform custom health checks to determine if a pod is healthy or not.

2. **Types of Probes**:
   - **Liveness Probe**: Determines if the container is running and healthy. If the probe fails, Kubernetes restarts the container.
   - **Readiness Probe**: Determines if the container is ready to accept traffic. If the probe fails, the pod is removed from the service’s endpoint list.
   - **Startup Probe**: Delays the execution of liveness and readiness probes until the container indicates it is ready. Useful for applications that take a long time to start.

3. **Probe Mechanisms**:
   - **Exec Action**: Runs a command inside the container. The container is considered healthy if the command exits with a status code of `0`.
   - **HTTP GET Action**: Makes an HTTP request to a specified endpoint. The container is considered healthy if the response status code is between 200 and 399.
   - **TCP Socket Action**: Attempts to open a TCP connection to a specified port. The container is considered healthy if the connection is successful.
   - **gRPC Action**: Makes a gRPC health check request to a specified port.

4. **Probe Configuration Parameters**:
   - **initialDelaySeconds**: Time to wait before executing the first probe.
   - **periodSeconds**: Frequency at which the probe is executed.
   - **timeoutSeconds**: Time after which the probe is considered failed if no response is received.
   - **failureThreshold**: Number of times the probe can fail before the container is marked as unhealthy.
   - **successThreshold**: Number of times the probe must succeed for the container to be marked as healthy.

5. **Hands-On Demonstration**:
   - The video demonstrates how to define and configure probes in a Kubernetes StatefulSet YAML file.
   - It shows how to intentionally make a probe fail (e.g., by using an incorrect command) and observe Kubernetes restarting the pod or removing it from the service’s endpoint list.
   - It also demonstrates how to fix the probe and observe the pod returning to a healthy state.

6. **Best Practices**:
   - Probes should be lightweight and avoid expensive operations.
   - Probes should be configured with appropriate frequency to avoid resource wastage or delayed detection of failures.
   - Regularly review and update probes when making changes to the application.

---

### **Hands-On Steps from the Video**

1. **Define a Liveness Probe**:
   - Add a `livenessProbe` to the container definition in the YAML file.
   - Use the `exec` action to run a command (e.g., `ping`).
   - Configure parameters like `initialDelaySeconds`, `periodSeconds`, and `failureThreshold`.

   Example:
   ```yaml
   livenessProbe:
     exec:
       command:
         - ping
         - db
     initialDelaySeconds: 1
     periodSeconds: 10
     failureThreshold: 3
   ```

2. **Intentionally Make the Probe Fail**:
   - Change the command to an invalid one (e.g., `ping db1`).
   - Apply the YAML file and observe Kubernetes restarting the pod.

3. **Define a Readiness Probe**:
   - Add a `readinessProbe` to the container definition.
   - Use the same configuration as the liveness probe but with a different purpose.

   Example:
   ```yaml
   readinessProbe:
     exec:
       command:
         - ping
         - db
     initialDelaySeconds: 1
     periodSeconds: 10
     failureThreshold: 3
   ```

4. **Observe the Readiness Probe in Action**:
   - Intentionally make the readiness probe fail (e.g., by using an invalid command).
   - Observe Kubernetes removing the pod from the service’s endpoint list.

5. **Define a Startup Probe**:
   - Add a `startupProbe` to the container definition.
   - Use the same configuration as the liveness probe but with a different purpose.

   Example:
   ```yaml
   startupProbe:
     exec:
       command:
         - ping
         - db
     initialDelaySeconds: 1
     periodSeconds: 10
     failureThreshold: 3
   ```

6. **Observe the Startup Probe in Action**:
   - Intentionally make the startup probe fail (e.g., by using an invalid command).
   - Observe Kubernetes restarting the pod and entering a `CrashLoopBackOff` state.

7. **Fix the Probes**:
   - Correct the probe commands (e.g., change `db1` back to `db`).
   - Apply the YAML file and observe the pod returning to a healthy state.

---

### **Summary of Probes**

| Probe Type       | Purpose                                                                 | Action on Failure                          |
|------------------|-------------------------------------------------------------------------|-------------------------------------------|
| **Liveness Probe** | Checks if the container is running and healthy.                         | Restarts the container.                    |
| **Readiness Probe**| Checks if the container is ready to accept traffic.                     | Removes the pod from the service’s endpoints. |
| **Startup Probe**  | Delays liveness and readiness probes until the container is ready.      | Restarts the container.                    |

---

### **Best Practices**
- Use probes only when necessary.
- Keep probes lightweight and fast.
- Regularly review and update probes as the application evolves.
- Avoid using `restartPolicy: Never` unless absolutely necessary, as it can leave containers in a failed state indefinitely.

