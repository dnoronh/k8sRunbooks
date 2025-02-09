## High CPU Usage Runbook

### Overview
This runbook provides steps to diagnose and resolve high CPU usage in a Kubernetes pod.

### Steps to Resolve

1. **Validate Autoheal Status in ArgoCD**:
   - Visit the [ArgoCD Dashboard](http://argocd.mycompany.com)
   - Check if autoheal is disabled and enable it if necessary.

2. **Identify the High CPU Consuming Pod**:
   - Use the command below to identify the pod with high CPU usage:
     ```sh
     kubectl top pod --namespace <namespace>
     ```
   - Replace `<namespace>` with the value from the alert.

3. **Analyze Pod Logs**:
   - Check the logs for any anomalies:
     ```sh
     kubectl logs <pod_name> --namespace <namespace>
     ```
   - Replace `<pod_name>` and `<namespace>` with the values from the alert.

4. **Increase Pod CPU Resources**:
   - Manually bump up the CPU resources for the pod:
     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: <pod_name>
       namespace: <namespace>
     spec:
       containers:
       - name: <container_name>
         resources:
           limits:
             cpu: "500m"
           requests:
             cpu: "250m"
     ```
   - Apply the configuration:
     ```sh
     kubectl apply -f <updated_configuration_file>.yaml
     ```

5. **Inform Team**:
   - Update the alert thread regarding the changes made.

6. **Persist Changes in Git Repository**:
   - Commit and push the changes to the manifest branch:
     - Repository: [Manifests Pod](http://www.github.com/manifests-pod)

7. **Long-Term Issue?**:
   - If the issue persists:
     - Check the [Application Dashboard](http://tools.grafana.com/application)
     - Raise an incident in [Rootly](http://www.rootly.com)

### Additional Resources
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/)
