# GitOps 
* The Continuous Deployment (CD) part by deploying a Docker image to a Kubernetes environment using ArgoCD
* Using Jenkins for building and pushing the Docker image 

## ArgoCD 



## Kubernetes 



### 2. **Test Connectivity to the API Server**
You can test connectivity to the API server using `curl`. Try both HTTP and HTTPS to see if one of them responds:

```bash
curl http://34.130.233.75:6443/version
curl https://34.130.233.75:6443/version
```

### 3. **Check Cluster Configuration**
If you have access to the Kubernetes master node, verify the API server configuration to ensure that the correct URL and port are being used. Look for the `--bind-address` and `--secure-port` flags in the API server configuration or command-line arguments.

### 4. **Consider Regenerating the Kubeconfig File**
If you have access to the Kubernetes master node, you may want to regenerate the `kubeconfig` file using a command like:

```bash
kubectl config view --raw > /path/to/new/kubeconfig.yaml
```

Then copy this file to the ArgoCD VM and use it as the `kubeconfig`.

---
## Connect ArgoCD (argocd's VM) to Kubernetes Cluster (K8s's VM)

argocd@argocd:~/.kube$ kubectl --kubeconfig=/home/argocd/.kube/app-cluster get nodes
NAME   STATUS   ROLES                  AGE    VERSION
k8s    Ready    control-plane,master   5d1h   v1.27.4+k3s1

### K8s Nodes
k8s@k8s:~/.kube$ kubectl get nodes 
NAME   STATUS   ROLES                  AGE    VERSION
k8s    Ready    control-plane,master   5d1h   v1.27.4+k3s1