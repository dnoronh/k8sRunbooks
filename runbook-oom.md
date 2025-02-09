## Pod Out of Memory Runbook

### Steps to Resolve

1. **Validate Autoheal Status in ArgoCD**:
   - Visit the [ArgoCD Dashboard](http://argocd.mycompany.com)
   - Check if autoheal is disabled.

2. **Manually Bump Up Pod Memory**:
   - Increase the pod memory allocation manually.
   - Ensure the changes are effective.

3. **Inform Team**:
   - Update the alert thread about the changes made.

4. **Persist Changes in Git Repository**:
   - Commit and push the changes to the manifest branch:
     - Repository: [Manifests Pod](http://www.github.com/manifests-pod)

5. **Long-Term Issue?**:
   - If the issue persists, perform the following:
     - Check the [Clickout Dashboard](http://tools.grafana.com/clickouts)
     - If needed, raise an incident in [Rootly](http://www.rootly.com)
